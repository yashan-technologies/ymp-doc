## Source Environment Preparation

### YashanDB Authorization

Depending on the migration mode, execute the following corresponding statements in YashanDB for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

The INITIAL mode requires the tool user to have the following privileges in YashanDB: privilege to create sessions, privilege to alter session parameters, privilege to query active transactions, privilege to query flashback SCN, privilege to query database parameters, privilege to flashback query schema information, privilege to flashback query table information, privilege to flashback query column information, privilege to flashback query primary key constraints, privilege to query tables to be synchronized, privilege to flashback query tables to be synchronized, privilege required for YStream usage, privilege to query YStream views, privilege to query table sizes of tables to be synchronized, and privilege for table partitioning during the full data synchronization phase.

```sql
GRANT CREATE SESSION TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON GV_$TRANSACTION TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT FLASHBACK ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT FLASHBACK ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT FLASHBACK ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT FLASHBACK ON DBA_CONS_COLUMNS TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user; 
GRANT FLASHBACK ANY TABLE TO dmc_user; 
GRANT YSTREAM_CAPTURE TO dmc_user;
GRANT SELECT ON V_$YSTREAM_SERVER TO dmc_user;
GRANT SELECT ON DBA_SEGMENTS TO dmc_user;
GRANT FLASHBACK ON DBA_SEGMENTS TO dmc_user;
GRANT SELECT ON DBA_LOBS TO dmc_user;
GRANT FLASHBACK ON DBA_LOBS TO dmc_user;
GRANT SELECT ON V_$DATAFILE TO dmc_user;
GRANT FLASHBACK ON  SYS.ALL_TAB_COLS TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.SCHEMATA TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.TABLES TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.PARTITIONS TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.COLUMNS  TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.TABLE_CONSTRAINTS TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.KEY_COLUMN_USAGE TO dmc_user;
```

== METADATA

The METADATA mode requires the tool user to have the following privileges in YashanDB: privilege to create sessions, privilege to alter session parameters, privilege to query all existing TABLEs, privilege to query table information in system views, and privilege to view metadata.

```sql
GRANT CREATE SESSION TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT SELECT_CATALOG_ROLE TO dmc_user;
GRANT SELECT ON *.* TO 'dmc_user'@'%';
```

== SNAPSHOT

The SNAPSHOT mode requires the tool user to have the following privileges in YashanDB: privilege to create sessions, privilege to alter session parameters, privilege to query data for tables to be synchronized (replace "*.*" with specific schema and table), privilege to query table information in system views, and privilege to view metadata.

```sql
GRANT CREATE SESSION TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON *.* TO 'dmc_user'@'%';
GRANT SELECT ANY TABLE TO dmc_user;
GRANT SELECT_CATALOG_ROLE TO dmc_user;
```

== INCREMENT

The INCREMENT mode requires the tool user to have the following privileges in YashanDB: privilege to create sessions, privilege to alter session parameters, privilege to query active transactions, privilege to query flashback SCN, privilege to query database parameters, privilege to flashback query schema information, privilege to flashback query table information, privilege to flashback query column information, privilege to flashback query primary key constraints, privilege required for YStream usage, privilege to query YStream views.

```sql
GRANT CREATE SESSION TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON GV_$TRANSACTION TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT SELECT ON DBA_LOG_GROUPS TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT FLASHBACK ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT FLASHBACK ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT FLASHBACK ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT FLASHBACK ON DBA_CONS_COLUMNS TO dmc_user;
GRANT YSTREAM_CAPTURE TO dmc_user;
GRANT SELECT ON V_$YSTREAM_SERVER TO dmc_user;
GRANT FLASHBACK ON  SYS.ALL_TAB_COLS TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.SCHEMATA TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.TABLES TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.PARTITIONS TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.COLUMNS  TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.TABLE_CONSTRAINTS TO dmc_user;
GRANT FLASHBACK ON INFORMATION_SCHEMA.KEY_COLUMN_USAGE TO dmc_user;
```

:::

### Database Parameter Adjustment

Incremental data relies on YStream to obtain committed data from YashanDB in real-time. You must first configure the STREAM_POOL_SIZE parameter value in YashanDB to allocate a memory pool for YStream.

For large transactions, it is recommended to increase this parameter; otherwise, the task will fail.

Setting the STREAM_POOL_SIZE larger than the SHARE_POOL_SIZE will be ineffective. If it exceeds this limit, first increase the SHARE_POOL_SIZE parameter value.

### System Variable Adjustment

If you need to migrate BLOB/CLOB data types, adjust the max_allowed_packet system variable to the maximum value. Use the following statement for settings:

```sql
SET GLOBAL max_allowed_packet = 1073741824;
```

### Enable Supplemental Logging

Incremental data synchronization requires Yashan Database to enable supplemental logging. The method is as follows(Full database additional logging is recommended as a priority):

```sql
ALTER SESSION SET COMPAT_VECTOR = yashan;-- Applicable solely IN MySQL compatibility mode

-- ENABLE SUPPLEMENTAL LOGGING FOR the entire DATABASE
ALTER DATABASE ADD SUPPLEMENTAL LOG TABLE TYPE (HEAP);
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;

-- Enable table-level supplemental logging
ALTER TABLE tablename ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;

ALTER SESSION SET COMPAT_VECTOR = mysql;  -- Applicable solely IN MySQL compatibility mode
```

### Statistics Collection

For faster metadata synchronization, it is recommended to collect statistics of system tables before starting the migration task. The statement is as follows:

```sql
exec DBMS_STATS.GATHER_SCHEMA_STATS('SYS', 1, FALSE, 'FOR ALL COLUMNS SIZE auto', 4, 'AUTO', TRUE);
```

The current version of YashanDB does not support using indexes for flashback queries. If you have not completed the above steps, it may result in unusually slow metadata synchronization.

## Target Environment Preparation

### MySQL Authorization

Depending on the migration mode, execute the following corresponding statements in MySQL for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

```sql
-- Privilege to query all existing SCHEMAS
GRANT SHOW DATABASES ON *.* TO 'dmc_user'@'%';   
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user'@'%';
-- Privilege required to delete or truncate tables
GRANT DROP ON *.* TO 'dmc_user'@'%';
-- Privileges required for inserting, updating, altering tables, and deleting table data
GRANT INSERT, UPDATE, DELETE, ALTER ON *.* TO 'dmc_user'@'%';
```

== METADATA

```sql
-- Privilege to query all existing SCHEMAS
GRANT SHOW DATABASES ON *.* TO 'dmc_user'@'%';   
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user'@'%';
-- Privilege required to delete or truncate tables
GRANT DROP ON *.* TO 'dmc_user'@'%';
```

== SNAPSHOT

```sql
-- Privilege to query all existing SCHEMAS
GRANT SHOW DATABASES ON *.* TO 'dmc_user'@'%';   
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user'@'%';
-- Privilege required to delete or truncate tables
GRANT DROP ON *.* TO 'dmc_user'@'%';
-- Privilege required for inserting into tables
GRANT INSERT ON *.* TO 'dmc_user'@'%';
```

== INCREMENT

```sql
-- Privilege to query all existing SCHEMAS
GRANT SHOW DATABASES ON *.* TO 'dmc_user'@'%';   
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user'@'%';
-- Privilege required to delete or truncate tables
GRANT DROP ON *.* TO 'dmc_user'@'%';
-- Privileges required for inserting, updating, altering tables, and deleting table data
GRANT INSERT, UPDATE, DELETE, ALTER ON *.* TO 'dmc_user'@'%';
```

:::

### Create Database

During the migration task execution, two auxiliary tables will be created in the target database for resumable transmission. The database name used can be specified in the task configuration file under sink.options.connectorParameter.auxInfoUseDatabase, or the default value $YDS_AUX_INFO can be used. Users should prepare this database in the target database in advance.

### Database Parameter Adjustment

1. If you need to migrate LOB data types, adjust the max_allowed_packet parameter to the maximum value. You can set it with the following statement:

```sql
SET GLOBAL max_allowed_packet = 1073741824;
```

2. Set the value of the lower_case_table_names parameter to either 0 or 1, and use the following statement for settings:

```sql
-- Query the system parameter values
show variables like 'lower_case_table_names';
-- Modify the value if it is not 0 or 1
SET GLOBAL lower_case_table_names = 1;
```
