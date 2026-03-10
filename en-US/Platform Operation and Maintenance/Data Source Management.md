The Data source management functionality is used to manage all databases involved in the YMP service, including: source database, target database, and assessment database (if the assessment built-in database is selected, no addition is required; if specified as an external database, it must be added to the Data source for assessment use, **the built-in database is used by default**).

> **Note**：
> 
> When the data source is selected as the source database by the current or other tasks, it cannot be used simultaneously as a built-in database, assessment database, or target database.

## Adding a Data Source

In the YMP data source management, add the connection information for the source and target databases, and assign permissions to the connection user as needed.

Click **[ + add data source ]** in the upper right corner, fill in the basic information, and then click **[ test connection ]**. The data source can only be successfully added if the test connection is successful.

When adding a data source, the version and connection information of the data source will be obtained simultaneously.

Explanation of the fields for adding a data source:

- Data source name: A unique data source name within the system.
- Data source type: Supports Oracle, MySQL, YashanDB, DM, PG (PG currently only supports assessment and metadata migration).
- Multi-tenant: This item can be configured when the data source type is Oracle, and the default value is No.
  - If switched to **[Yes]**: CDB information must be filled in, and PDB is optional. When only CDB is filled in, the migration scope covers all data under that CDB. **For incremental migration, the SID connection method must be used, and the source PDB name must be filled in.**
  - If switched to **[No]**: just fill in the database name.
- Host IP: The IP address where the database is located.
- Port: The database listening port.
- Database: Optional; required for Oracle and PG; optional for MySQL, DM, YashanDB.
- Username: Database connection user; this user must have the appropriate privileges for data migration. Specific privilege requirements will be detailed in [Data Migration](../Data Migration/Overview) for various scenarios.
- Password: Password for the database connection user.
- Is Normal User: Supported only for Oracle and DM data sources; defaults to non-normal user. Specific requirements will be detailed in [Full Migration](../Data Migration/Overview) for various scenarios.

## Modifying a Data Source

Click **[ Edit ]**, fill in the modification information, and then click **[ test connection ]**. The data source information can only be successfully modified if the test connection is successful.

When modifying the data source, it will check whether the data source is associated with migration and assessment tasks:

- If not associated, you are allowed to modify the IP, port number, database name, database username, and database user password.
- If associated, only the database user password can be modified.

## Deleting a Data Source

> **Caution**:
>
> Once deleted, it cannot be directly restored and can only be re-added.

Clicking **[ delete ]** will verify whether the data source is associated with migration and assessment tasks. If it is associated, it cannot be deleted directly and must wait until the task is completed before deletion.