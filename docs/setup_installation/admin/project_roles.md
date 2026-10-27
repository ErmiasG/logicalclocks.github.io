---
description: Define custom project roles that remove permissions from Data owner or Data scientist, give them to project members and group mappings, and what a member holding one is refused
---

# Custom Project Roles { #custom-project-roles }

## Introduction

Every member of a project holds a project role.
Hopsworks has four built-in roles, Data owner, Data scientist, Observer and Feature store restricted, described in [How To Manage Members To A Project][how-to-manage-members-to-a-project].

A custom project role is a Data owner or a Data scientist with some permissions removed.
A cluster administrator defines it once, and Data owners can then give it to members in every project, like a built-in role.
A custom role never allows more than the role it is based on, and it always allows what an Observer can do.

A member holding a custom role is refused every request that needs a permission the role removed, with HTTP 403.
Most refusals carry status code 150068 and name the permission:

```json
{
  "errorCode": 150068,
  "usrMsg": "Your project role does not allow Kafka: Manage schemas"
}
```

## Prerequisites

You need to be an administrator on a Hopsworks cluster.

## Enable custom project roles { #enable-custom-project-roles }

Custom project roles are off by default.
Turn them on with the `enable_custom_project_roles` variable in the Helm values:

```yaml
hopsworks:
  variables:
    enable_custom_project_roles: "true"
```

The chart writes the variable on every install and upgrade, so a value set on the [Cluster Configuration][cluster-configuration] page lasts only until the next upgrade.

With the variable on, _Project Roles_ appears under _Security & Access_ in _Cluster Settings_, and the role pickers for project members and group mappings list the custom roles.
With the variable off again, custom roles can no longer be given.
Members who already hold one keep it and are still refused what it removed.

!!! warning "Rolling upgrades"
    A Hopsworks instance running a release without custom project roles does not know them, and lets a member holding one do everything the base role allows.
    Give members custom roles only once every Hopsworks instance runs the new release.

## Create a custom role { #create-a-custom-project-role }

Click your name in the top right corner of the navigation bar, choose _Cluster Settings_, then choose _Project Roles_ under _Security & Access_ in the left sidebar.
The list shows the built-in roles next to the custom ones, with how many members hold each role.
Click _New project role_, or _Duplicate_ on Data owner, Data scientist or a custom role to start from it.

Fill in the role:

- _Name_: up to 32 letters, digits, spaces, `-` and `_`.
  The names of the built-in roles are reserved, whatever their case and separators, so `data_owner` is refused too.
- _Description_: optional, up to 512 characters.
- _Based on_: Data owner or Data scientist.
  It cannot be changed once the role exists, because members holding the role keep the base role in every system outside Hopsworks' own checks.
- _Permissions_: one row per project service and one column per action.
  Switch off what members holding the role must not do.

The _Summary_ shows which role the new one behaves as and what it removes.
Click _Create role_.
A role must remove at least one permission, otherwise it would be its base role under another name.

### What the permission cells mean

| Cell | Meaning |
| --- | --- |
| Allowed or Removed | A permission the role can remove. |
| Always allowed | What an Observer can do. Every project role keeps it. |
| Follows the base role | Also granted by a system outside Hopsworks' own checks, named in the tooltip. A custom role cannot remove it. |
| Not part of this role | The base role does not have it, for example _Edit & delete others'_ feature views for a Data scientist. |

A permission follows the base role when a member can exercise it without going through Hopsworks, so removing it in Hopsworks alone would not stop them:

| Service | Permission | Also granted through |
| --- | --- | --- |
| Files & datasets | Upload & create, Delete (editable datasets) | HopsFS |
| Feature groups | Insert data | Kafka and HopsFS |
| Tags | Write tag values | HopsFS, where a tag is an extended attribute on the entity's inode |
| Model registry | Register & edit, Delete | HopsFS |
| Model inference | Send predictions | The model serving authenticator |
| Airflow | Edit DAGs, Delete DAGs | HopsFS and the Airflow plugin |
| SQL queries | Run SQL | Trino JDBC |
| Superset | View dashboards, Edit dashboards | Superset's own roles |
| Kafka | Create topics | Kafka clients |

Data validation and feature monitoring create and run jobs.
Switching off _Jobs: Create & edit_ or _Jobs: Run & stop_ therefore also switches off _Data validation: Run validation_ and _Feature monitoring: Create & edit configs_, and neither can be switched back on while a job permission is off.
The chat app, the evaluation job and the feedback review job of an agent deployment are jobs too, so creating or changing one also needs _Jobs: Create & edit_, and starting one _Jobs: Run & stop_.
The evaluation job Hopsworks creates with default settings, when the deployment is created or its first evaluation run starts, does not need _Jobs: Create & edit_.

### What a removed permission does

- A request that needs the permission is refused, in the UI, the Python client and the REST API alike.
- Jupyter servers, jobs and deployments that were already running keep running.
  Starting new ones is refused.
- A scheduled job runs as the member who created it.
  While their role does not allow _Jobs: Run & stop_, each scheduled run is refused and logged, and the schedule is kept, so the job runs again if the permission comes back.
- Feature views and training datasets distinguish who created them: _Edit & delete own_ covers the member's own, _Edit & delete others'_ the ones other members created.

## Give a custom role to members { #give-a-custom-project-role }

In a project, a Data owner picks a custom role under _Custom roles_ in the _Add members_ dialog or in a member's role picker, as described in [How To Manage Members To A Project][how-to-manage-members-to-a-project].
A member can give only roles that fit inside their own role, and change only members whose current role fits inside their own: every permission of the other role must be one their own role has.
A built-in Data owner holds every permission, so this limits only members whose own role is a custom one.

A group mapping can give a custom role as its _Project role_, for [Hopsworks groups][configure-group-to-project-mapping], [LDAP groups][configure-ldapkerberos-group-to-project-mapping] and [OAuth2 groups][configure-oauth2-group-to-project-mapping].
When several mappings put a user in the same project, the user gets the role with the highest base role (Data owner, then Data scientist, then Observer), then the one that removes the fewest permissions, so a built-in role beats a custom role on the same base, then the oldest role.

## Change or delete a custom role { #change-a-custom-project-role }

Open the role from the list to see its permissions, the members that hold it and the group mappings that give it.

- A role that a member holds cannot be edited or deleted.
  Duplicate it, change the copy, and give the copy to the members instead.
- A role that a group mapping gives can be edited while no member holds it, but not deleted.
  Change the mapping's role first.
- Built-in roles cannot be changed.
  Duplicate Data owner or Data scientist to start a custom role from them.

## REST API

Custom roles are managed under `/hopsworks-api/api/admin/project-roles`, with an administrator's token or an API key with the `ADMIN` scope.

| Method | Path | Does |
| --- | --- | --- |
| `GET` | `/admin/project-roles` | Lists the built-in and custom roles, with where each is used. |
| `GET` | `/admin/project-roles/catalog` | Lists the services and actions, and which can be removed. |
| `GET` | `/admin/project-roles/{id}` | Returns one role. |
| `POST` | `/admin/project-roles` | Creates a role. |
| `PUT` | `/admin/project-roles/{id}` | Changes a role, given the `version` it was read at. |
| `DELETE` | `/admin/project-roles/{id}` | Deletes a role. |

A role lists the permissions it removes, each a service `id` and an `action` from the catalog:

```bash
curl -X POST "https://<hopsworks>/hopsworks-api/api/admin/project-roles" \
  -H "Authorization: ApiKey $HOPSWORKS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "ML engineer",
        "description": "Builds feature views and models, no notebooks",
        "baseRole": "Data scientist",
        "removedPermissions": [{"service": "JUPYTER", "action": "EXTRA"}]
      }'
```

The status codes the endpoints answer with are listed under `ProjectRoleErrorCode` in [REST API Status Codes][rest-api-status-codes].
