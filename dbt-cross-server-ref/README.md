# dbt-cross-server-ref
A dbt-core extension package that enables reading from and writing to multiple SQL Server instances via linked servers.

### cross_server_ref – Reference Tables Across Linked SQL Servers ([source](macros/cross_server_ref.sql)
This macro allows you to reference tables across SQL Server linked servers or your current target environment dynamically, using variables defined in dbt_project.yml.

Usage

```
SELECT DISTINCT MstrARIdfr AS agreement_identifier
FROM {{ cross_server_ref('source', 'source_table') }}
```
When configured correctly, this resolves to:


[LINKED_SERVER_NAME].[SOURCE_DATABASE].[SOURCE_SCHEMA].[source_table]

For referencing your target tables, use:

```
SELECT * FROM {{ cross_server_ref('my_model_name', target_flag=true) }}
```

Or override schema:

```
SELECT * FROM {{ cross_server_ref('my_model_name', target_flag=true, target_schema='custom_schema') }}
```

dbt_project.yml Setup

```
vars:
  linked_servers:
    source: "LINKED_SERVER_NAME"  # Use lowercase key
  source_databases:
    source: "SOURCE_DATABASE"
  source_schemas:
    source: "SOURCE_SCHEMA"
```
Make sure the source key matches the lowercase form of the source system name used in the macro call.

Source Declaration (Optional)
If you're defining sources: for documentation or consistency, use:

```
version: 2
sources:
  - name: SOURCE
    database: SOURCE_DATABASE
    schema: source_schema
    tables:
      - name: source_table_name
```

Error Handling

If linked_servers[source] is missing, a compile-time error is raised:
```
Missing linked_server for source: source. Define in dbt_project.yml vars: linked_servers
```
If neither target_flag nor source_name is provided correctly:


Invalid cross_server_ref parameters. Use either:
1. cross_server_ref('source_name', 'table_name') for sources
2. cross_server_ref('table_name', target_flag=true) for targets

##  Installation

Add this to your project’s `packages.yml`:

```yaml
packages:
  - package: bssulfikkar/dbt-cross-server-ref
    version: [">=0.1.0", "<0.2.0"]
```

Then run:

```
dbt deps
```
