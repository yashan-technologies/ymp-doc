## Source Environment Preparation

### MySQL Authorization

According to different migration modes, execute the following corresponding statements in MySQL for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

```sql
-- Privilege to query all existing SCHEMA
GRANT SHOW DATABASES ON *.* TO 'dmc_user'@'%';   
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privileges required for master-slave replication for MySQL connection user
GRANT REPLICATION slave ON *.* TO 'dmc_user'@'%';
GRANT REPLICATION client ON *.* TO 'dmc_user'@'%';
-- Privilege required to operate FTWRL lock in locked synchronization
GRANT RELOAD ON *.* TO 'dmc_user'@'%';

-- When task configuration options->heartbeatEnabled= true, the following privileges are also required
-- Privilege required to create heartbeat table
GRANT CREATE ON *.* TO 'dmc_user'@'%';
-- Privilege required to insert and update heartbeat table
GRANT INSERT, UPDATE ON *.* TO 'dmc_user'@'%';
```

== METADATA

```sql
-- Privilege to query all existing SCHEMA
GRANT SHOW DATABASES ON *.* TO 'dmc_user'@'%';   
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privilege required to operate FTWRL lock in locked synchronization
GRANT RELOAD ON *.* TO 'dmc_user'@'%';
```

== SNAPSHOT

```sql
-- Privilege to query data from the tables to be synchronized ("*.*" can be replaced by specific schema and table)
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privilege required to operate FTWRL lock in locked synchronization
GRANT RELOAD ON *.* TO 'dmc_user'@'%';
```

== INCREMENT

```sql
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user'@'%';
-- Privileges required for master-slave replication for MySQL connection user
GRANT REPLICATION slave ON *.* TO 'dmc_user'@'%';
GRANT REPLICATION client ON *.* TO 'dmc_user'@'%';
-- Privilege required to operate FTWRL lock in locked synchronization
GRANT RELOAD ON *.* TO 'dmc_user'@'%';

-- When task configuration options->heartbeatEnabled= true, the following privileges are also required
-- Privilege required to create heartbeat table
GRANT CREATE ON *.* TO 'dmc_user'@'%';
-- Privilege required to insert and update heartbeat table
GRANT INSERT, UPDATE ON *.* TO 'dmc_user'@'%';
```

:::

### Enable MySQL Binlog

MySQL Stream encapsulates the MySQL binlog connector to obtain MySQL incremental data in real-time.

When the migration task starts, it will check the status of MySQL binlog. If it is not enabled, the task will fail to start.

To enable it, open the MySQL my.cnf configuration file and add the following content under [mysqld], then restart the database. For details on my.cnf file configuration, please refer to the MySQL official website.

```toml
# Specify the master server-id
server-id         = 223344
log_bin           = mysql-bin
binlog_format     = ROW
# There are three modes for binlog_row_image; DMC supports only FULL mode
binlog_row_image  = FULL
```

After restarting, execute the following statement in MySQL to check if it has been successfully enabled:

```sql
-- The result is ON, indicating that MySQL binlog is enabled
show variables like '%log_bin%';
```

Please ensure that the binlog log files are not deleted, as it will affect the currently running synchronization tasks.

### Create Database

The migration task will create a heartbeat table in the source database, and the name of the database used can be specified in the task configuration file under source.options.connectorParameter.auxInfoUseDatabase, or the default value $YDS_AUX_INFO can be used. Users must prepare this database in advance on the source side.

### Database Parameter Adjustment

1. If LOB type data needs to be migrated, set the max_allowed_packet parameter to the maximum value. Use the following statement for settings:

```sql
SET GLOBAL max_allowed_packet = 1073741824;
```

2. Set the value of the lower_case_table_names parameter to 0 or 1, using the following statements for reference:

```sql
-- Query the system parameter value
show variables like 'lower_case_table_names';
-- If it is not 0 or 1, modify the value
SET GLOBAL lower_case_table_names = 1;
```

## Target Environment Preparation

### YashanDB Authorization

According to different migration modes, execute the following corresponding statements in YashanDB for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

```sql
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user';
-- Privilege required to drop or truncate tables
GRANT DROP ON *.* TO 'dmc_user';
-- Privileges required to insert, update, and delete table data
GRANT INSERT,UPDATE,DELETE ON *.* TO 'dmc_user';
-- Privilege required to execute DDL related to tables
GRANT ALTER ON *.* TO 'dmc_user';
-- Privilege required to query database parameters
GRANT SELECT ON V_$PARAMETER TO dmc_user;
```

== METADATA

```sql
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user';
-- Privilege required to drop or truncate tables
GRANT DROP ON *.* TO 'dmc_user';
-- Privilege required to query database parameters
GRANT SELECT ON V_$PARAMETER TO dmc_user;
```

== SNAPSHOT

```sql
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user';
-- Privilege required to drop or truncate tables
GRANT DROP ON *.* TO 'dmc_user';
-- Privilege required to insert data into tables
GRANT INSERT ON *.* TO 'dmc_user';
-- Privilege required to query database parameters
GRANT SELECT ON V_$PARAMETER TO dmc_user;
```

== INCREMENT

```sql
-- Privilege to query table information in system views
GRANT SELECT ON *.* TO 'dmc_user';
-- Privilege required to create tables
GRANT CREATE ON *.* TO 'dmc_user';
-- Privilege required to drop or truncate tables
GRANT DROP ON *.* TO 'dmc_user';
-- Privileges required to insert, update, and delete table data
GRANT INSERT,UPDATE,DELETE ON *.* TO 'dmc_user';
-- Privilege required to execute DDL related to tables
GRANT ALTER ON *.* TO 'dmc_user';
-- Privilege required to query database parameters
GRANT SELECT ON V_$PARAMETER TO dmc_user;
```

:::

### Create Database

During the migration task, two auxiliary tables for breakpoint continuation will be created in the target database. The database name used can be specified in the task configuration file under sink.options.connectorParameter.auxInfoUseDatabase, or the default value $YDS_AUX_INFO can be used. Users must prepare this database in advance in the target database.