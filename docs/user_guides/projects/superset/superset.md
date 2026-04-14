---
description: Guide on how to use Superset as a Hopsworks user
---

# Data exploration and data visualization (Superset)

Apache Superset is a modern data exploration and visualization platform integrated with Hopsworks.
It provides an intuitive interface for creating interactive dashboards, running SQL queries, and building advanced visualizations on top of your feature data.

## Prerequisites

To use Superset, you need:

- Membership in a Hopsworks project with Superset enabled
- Appropriate project role permissions (Data Owner or Data Scientist)

## Accessing Superset

Access Superset from your Hopsworks project:

1. Navigate to your project in Hopsworks
2. In the left sidebar, locate the **Analytics** section
3. Click on **Superset**

<figure>
  <img src="../../../../assets/images/guides/superset/project-superset.png" alt="Superset in project menu" />
  <figcaption>Accessing Superset from a Hopsworks project</figcaption>
</figure>

This opens the Superset interface with access to your project's data sources and any dashboards you have created or have permission to view.

## Superset Interface

The Superset home page provides access to all major features:

<figure>
  <img src="../../../../assets/images/guides/superset/superset-landing.png" alt="Superset home page" />
  <figcaption>Superset home page</figcaption>
</figure>

### Main Navigation

- **Home**: Landing page with recent activity and quick access
- **Dashboards**: View and create interactive dashboards
- **Charts**: Individual visualization components
- **Datasets**: Configure and manage data sources from Hopsworks
- **SQL**: Access SQL Lab for ad-hoc queries

### Recent Activity

The home page displays your recent activity organized by:

- **Viewed**: Recently accessed dashboards and charts
- **Edited**: Items you have recently modified
- **Created**: Your recently created content

This provides quick access to your most frequently used visualizations.

## SQL Lab

SQL Lab is an interactive SQL query interface for exploring your feature data.

### Opening SQL Lab

1. Click **SQL** → **SQL Lab** in the top navigation
2. Select your database from the **Database** dropdown (typically `trino`)
3. Select your project's feature store schema from the **Schema** dropdown

<figure>
  <img src="../../../../assets/images/guides/superset/sql-lab.png" alt="SQL Lab interface" />
  <figcaption>SQL Lab for running queries</figcaption>
</figure>

### Running Queries

To execute a query:

1. Type your SQL query in the editor
2. Click **Run** or press `Ctrl+Enter` (Windows/Linux) or `Cmd+Enter` (Mac)
3. View results in the **Results** tab below the editor
4. Check the **Query History** tab to see previous queries

### Query Features

- **Auto-complete**: SQL Lab provides auto-complete for table and column names
- **Query history**: All executed queries are saved and can be reviewed
- **Save queries**: Save frequently used queries for future use
- **Export results**: Download query results as CSV or other formats
- **Visualize results**: Create charts directly from query results

### Best Practices for SQL Lab

- **Limit result sets**: Use `LIMIT` clauses to avoid retrieving excessive data
- **Test queries incrementally**: Start with small queries and build up complexity
- **Save important queries**: Use the save feature for queries you'll reuse
- **Use proper formatting**: Format SQL for readability and maintainability

## Creating Datasets

Datasets define the data sources for your charts and dashboards.
They typically map to Feature Groups in your Hopsworks project.

### Adding a Dataset

1. Navigate to **Datasets**
2. Click **+ Dataset** in the top right
3. Select your database (e.g., `Trino__project1__meb10000_superset`)
4. Select your schema (your project's feature store schema)
5. Select the table (your Feature Group or Feature View)
6. Click **Create Dataset and Create Chart**

!!! note "Using Different Table Formats"
    If your feature store tables use a specific table format (Delta, Iceberg, or Hudi) and the Trino default catalog is not configured to match that format, you need to specify the catalog explicitly in your query.

    Click **Create dataset from SQL query** and specify the catalog name matching your table format:

    - For Delta format: `SELECT * FROM delta.<project_name>_featurestore.<table_name>`
    - For Iceberg format: `SELECT * FROM iceberg.<project_name>_featurestore.<table_name>`
    - For Hudi format: `SELECT * FROM hudi.<project_name>_featurestore.<table_name>`
    
    If you're unsure which format your tables use or what the default catalog is set to, contact your Hopsworks administrator.

### Configuring Datasets

After creating a dataset, you can configure:

- **Columns**: Define data types and column-specific settings
- **Metrics**: Create calculated metrics for aggregations
- **Calculated columns**: Define computed columns using SQL expressions
- **Filters**: Set default filters for the dataset

## Creating Charts

Charts are individual visualization components that can be added to dashboards.

### Creating a New Chart

1. Navigate to **Charts**
2. Click **+ Chart**
3. Select a dataset
4. Choose a visualization type (e.g., Bar Chart, Line Chart, Pie Chart, Table)
5. Click **Create new chart**

### Configuring Charts

The chart editor provides:

- **Data tab**: Configure metrics, dimensions, and filters
- **Customize tab**: Adjust colors, labels, and styling
- **Preview**: Real-time preview of your chart
- **Save**: Save the chart for use in dashboards

## Creating Dashboards

Dashboards combine multiple charts into interactive visualization reports.

### Creating a New Dashboard

1. Navigate to **Dashboards**
2. Click **+ Dashboard**
3. Enter a dashboard name
4. Click **Save**

### Adding Charts to Dashboards

1. Open your dashboard in edit mode
2. Click **Edit dashboard**
3. Drag and drop charts from the chart list onto the dashboard
4. Resize and arrange charts as needed
5. Click **Save** to publish the dashboard

## Sharing and Collaboration

### Sharing Dashboards

Share dashboards with other project members:

- Dashboards are automatically visible to all members of your Hopsworks project
- Users with appropriate permissions can view and interact with dashboards
- Data Owner and Data Scientist roles can edit dashboards

### Setting Permissions

Dashboard permissions are controlled through your Hopsworks project membership.
Only users who are members of the project can access project-specific data and dashboards.

#### Making Dashboards Public

You can make a dashboard accessible to all Superset users by adding the Public role to the dashboard:

1. Navigate to **Dashboards** and locate your dashboard
2. Click the **edit icon** menu next to the dashboard name
3. In the **Access** section, find the **Roles** dropdown
4. Select **Public** from the roles list
5. Click **Save** to apply the changes

<figure>
  <img src="../../../../assets/images/guides/superset/add-public-role.png" alt="Adding public role to dashboard" />
  <figcaption>Adding the Public role to make a dashboard accessible to all users</figcaption>
</figure>

When the Public role is added, the dashboard becomes viewable by anyone with access to Superset.

!!! warning "Security Considerations"
    Only add the Public role to dashboards containing non-sensitive data.
    Ensure the dashboard does not expose confidential or project-specific information.

### Sharing Dashboard Permalinks

Superset allows you to share a direct link to a dashboard, preserving the current view state including applied filters and parameter values.

To share a dashboard permalink:

1. Open the dashboard you want to share
2. Apply any filters or configure the view as desired
3. Click the **Share** button (share icon) in the top right of the dashboard
4. Select **Copy permalink to clipboard**
5. Share the copied URL with other users

The permalink captures:

- Current dashboard state and selected filters
- Active parameter values
- Visible chart configurations
- Dashboard layout and zoom level

!!! note "Permission Requirements"
    **For project members:** Users must be members of the same Hopsworks project to access the dashboard via permalink.

    **For unauthenticated users:** To share permalinks with unauthenticated users outside your project, the dashboard must have the Public role assigned (see [Making Dashboards Public][making-dashboards-public] above).
    If the Public role option is not available, contact your Hopsworks administrator to enable it.

### Exporting Dashboards

You can export dashboards for reporting or archival purposes:

1. Open the dashboard
2. Click the **...** menu in the top right
3. Select **Download** to export as PDF or image
4. Choose your preferred format and resolution
5. Save the exported file

Exported dashboards capture the current state of all charts and visualizations, making them suitable for reports, presentations, or documentation.

## Working with Feature Store Data

### Querying Feature Groups

Feature Groups are available as tables in SQL Lab:

```sql
SELECT 
    feature1,
    feature2,
    COUNT(*) as count
FROM delta.<project_name>_featurestore.my_feature_group
GROUP BY feature1, feature2
ORDER BY count DESC
LIMIT 100
```

### Joining Multiple Feature Groups

Combine data from multiple Feature Groups:

```sql
SELECT 
    fg1.user_id,
    fg1.user_features,
    fg2.transaction_features
FROM delta.<project_name>_featurestore.user_features fg1
JOIN delta.<project_name>_featurestore.transaction_features fg2
    ON fg1.user_id = fg2.user_id
WHERE fg1.created_date >= CURRENT_DATE - INTERVAL '30' DAY
LIMIT 100
```

## Performance Tips

### Query Optimization

- **Use selective filters**: Filter data as early as possible in queries
- **Limit result sets**: Always use `LIMIT` to prevent excessive data transfers
- **Leverage indexes**: Filter on indexed columns when possible
- **Avoid SELECT ***: Select only the columns you need

### Chart Performance

- **Reduce data points**: Use aggregation to reduce the number of data points
- **Enable caching**: Cache chart results for frequently accessed dashboards
- **Set appropriate refresh intervals**: Don't refresh more frequently than necessary
- **Use time-based filters**: Limit data to relevant time periods

## Troubleshooting

### Common Issues

**Cannot see my Feature Groups:**

- Verify you are connected to the correct database and schema
- Ensure you have appropriate project permissions
- Refresh the schema list if Feature Groups were recently created

**Queries timing out:**

- Reduce the amount of data being queried using filters
- Add `LIMIT` clauses to restrict result set size
- Break complex queries into smaller steps
- Contact your administrator about timeout settings

**Charts not loading:**

- Check that the underlying dataset is still available
- Verify you have permissions to access the data source
- Refresh the page or clear browser cache
- Check query filters for errors

**Dashboard filters not working:**

- Ensure filters are properly connected to charts
- Verify filter types match the chart data types
- Check for filter conflicts or invalid filter combinations

**Trino table type error:**

```text
trino error: TrinoExternalError(type=EXTERNAL, name=UNSUPPORTED_TABLE_TYPE,
message="Cannot query Delta Lake table 'project1_featurestore.transactions_1'",
query_id=20260414_155131_00049_qgyn2)
This may be triggered by:
Issue 1002 - The database returned an unexpected error.
```

- This error occurs when querying tables without specifying the catalog name
- Your feature store tables use a specific format (Delta, Iceberg, or Hudi) that requires an explicit catalog prefix
- Solution: Specify the catalog name in your query (e.g., `delta.project1_featurestore.table_name`)
- See [Using Different Table Formats][adding-a-dataset] for detailed instructions on creating datasets with the correct catalog prefix
- If you're unsure which catalog to use, contact your Hopsworks administrator

## Best Practices

### Organizing Content

- **Use descriptive names**: Name dashboards and charts clearly
- **Tag and categorize**: Use tags to organize related content
- **Create folders**: Group related dashboards together
- **Document dashboards**: Add descriptions explaining the purpose and key metrics

### Performance

- **Keep queries simple**: Complex queries should be tested in SQL Lab first
- **Use appropriate chart types**: Choose visualizations that match your data
- **Limit dashboard size**: Too many charts can slow down loading
- **Schedule refreshes**: Use automated refresh during off-peak hours

### Data Quality

- **Validate queries**: Test queries thoroughly before creating charts
- **Handle null values**: Account for missing data in visualizations
- **Use appropriate aggregations**: Choose meaningful aggregation functions
- **Document data sources**: Note which Feature Groups are used in each dashboard

### Security

- **Respect data privacy**: Only share dashboards with authorized project members
- **Avoid sensitive data in names**: Don't include sensitive information in dashboard titles
- **Regular reviews**: Periodically review and clean up unused dashboards
- **Follow project policies**: Adhere to your organization's data governance policies
