## Source Environment Preparation

### Oracle Authorization

According to different migration modes, execute the following corresponding statements in Oracle for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

The INITIAL mode requires the tool user to have the following privileges in the Oracle database: connection privilege, create table privilege, privileges required to query container information, container switch privilege (when data is in PDB), privileges required to query database parameters, flashback query privileges to check if the schema exists, flashback query privileges to check if the table exists, flashback query privileges for column information views, flashback query privileges for primary key constraints, flashback query privileges for constraint column names, flashback query privileges for the size of tables to be synchronized, flashback query privileges for large field information of tables to be synchronized, flashback query privileges for database segment extension information (used for concurrent table split synchronization), privileges to query database object information (used for concurrent table split synchronization), privileges to query partition table subpartition information (used for concurrent table split synchronization), privileges to query partition table partition information (used for concurrent table split synchronization), LogMiner and related view operation privileges, privilege for the connecting user to create tables, default tablespace privilege for the connecting user, privileges required to create LOG MINER heartbeat table, private temporary table privileges, privileges for user quotas on tablespace size, and privileges required to query database parameters.

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required to query sequence information and perform flashback queries on sequence information.

When the task configuration options->heartbeatEnabled= true, the following additional privileges are required: privileges to create heartbeat table, insert and update heartbeat table privileges, and tablespace privilege required for inserting data into the heartbeat table.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required to query partition tables and perform flashback queries on partition tables.

```sql
-- When Oracle is in a non-container architecture, or when Oracle is in a container architecture but the data resides in the CDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT FLASHBACK ON DBA_USERS TO dmc_user;
GRANT FLASHBACK ON DBA_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_TAB_COLS TO dmc_user;
GRANT FLASHBACK ON DBA_CONSTRAINTS TO dmc_user;
GRANT FLASHBACK ON DBA_CONS_COLUMNS TO dmc_user;
GRANT FLASHBACK ON DBA_OBJECTS TO dmc_user;
GRANT FLASHBACK ON DBA_EXTENTS TO dmc_user;
GRANT FLASHBACK ON DBA_SEGMENTS TO dmc_user;
GRANT FLASHBACK ON DBA_LOBS TO dmc_user;
GRANT FLASHBACK ON DBA_TAB_SUBPARTITIONS TO dmc_user;
GRANT FLASHBACK ON DBA_TAB_PARTITIONS TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT FLASHBACK ANY TABLE TO dmc_user;
GRANT LOGMINING TO dmc_user;
GRANT SELECT_CATALOG_ROLE TO dmc_user;
GRANT EXECUTE_CATALOG_ROLE TO dmc_user;
GRANT CREATE TABLE TO dmc_user;
ALTER USER dmc_user QUOTA size ON USERS;
GRANT SELECT ON V_$PARAMETER TO dmc_user;

-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
-- When the task configuration options->heartbeatEnabled= true
GRANT CREATE ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;
ALTER USER dmc_user QUOTA UNLIMITED ON your_tablespacename;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;

-- When Oracle is in a container architecture and the data resides in a PDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SET CONTAINER TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_USERS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_TABLES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_TAB_COLS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_CONSTRAINTS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_CONS_COLUMNS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_OBJECTS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_EXTENTS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_SEGMENTS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_LOBS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_TAB_SUBPARTITIONS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_TAB_PARTITIONS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON V_$DATABASE TO dmc_user CONTAINER=ALL;
GRANT SELECT ANY TABLE TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ANY TABLE TO dmc_user CONTAINER=ALL;
GRANT LOGMINING TO dmc_user;
GRANT SELECT_CATALOG_ROLE TO dmc_user CONTAINER=ALL;
GRANT EXECUTE_CATALOG_ROLE TO dmc_user CONTAINER=ALL;
GRANT CREATE TABLE TO dmc_user;
ALTER USER dmc_user QUOTA size ON USERS;
GRANT SELECT ON V_$PARAMETER TO dmc_user CONTAINER=ALL;
-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user CONTAINER=ALL;
-- When the task configuration options->heartbeatEnabled= true
GRANT CREATE ANY TABLE TO dmc_user CONTAINER=ALL;
GRANT INSERT ANY TABLE TO dmc_user CONTAINER=ALL;
ALTER USER dmc_user QUOTA UNLIMITED ON your_tablespacename CONTAINER=ALL;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
```

Among them, the role authorization for `SELECT_CATALOG_ROLE` and `EXECUTE_CATALOG_ROLE` can be replaced with the following statement to directly grant permissions on the underlying views:

```sql
-- When Oracle is in a non-container architecture, or when Oracle is in a container architecture but the data resides in the CDB, it can be replaced with the following statement.
GRANT SELECT ON SYS.GV_$TRANSACTION TO username;
GRANT SELECT ON SYS.V_$LOGMNR_LOGS TO username;
GRANT SELECT ON SYS.V_$ARCHIVED_LOG TO username;
GRANT SELECT ON SYS.V_$LOGFILE TO username;
GRANT SELECT ON SYS.V_$LOG TO username;
GRANT SELECT ON DBA_USERS TO username;
GRANT SELECT ON SYS.V_$ARCHIVE_DEST_STATUS TO username;
GRANT SELECT ON SYS.V_$LOGMNR_CONTENTS TO username;
GRANT SELECT ON DBA_TABLES TO username;
GRANT SELECT ON DBA_TAB_COLS TO username;
GRANT SELECT ON DBA_CONSTRAINTS TO username;
GRANT SELECT ON DBA_CONS_COLUMNS TO username;
GRANT SELECT ON DBA_OBJECTS TO username;
GRANT SELECT ON DBA_EXTENTS TO username;
GRANT SELECT ON DBA_SEGMENTS TO username;
GRANT SELECT ON DBA_LOBS TO username;
GRANT SELECT ON DBA_TAB_SUBPARTITIONS TO username;
GRANT SELECT ON DBA_TAB_PARTITIONS TO username;
GRANT EXECUTE ON DBMS_LOGMNR TO username;
GRANT SELECT ON V_$PARAMETER TO username;
GRANT SELECT ON DBA_LOB_PARTITIONS TO username;
GRANT SELECT ON DBA_LOB_SUBPARTITIONS TO username;
-- When Oracle is in a container architecture and the data resides in a PDB, it can be replaced with the following statement.
GRANT SELECT ON SYS.GV_$TRANSACTION TO username CONTAINER=ALL;
GRANT SELECT ON SYS.V_$LOGMNR_LOGS TO username;
GRANT SELECT ON SYS.V_$ARCHIVED_LOG TO username;
GRANT SELECT ON SYS.V_$LOGFILE TO username;
GRANT SELECT ON SYS.V_$LOG TO username;
GRANT SELECT ON DBA_USERS TO username CONTAINER=ALL;
GRANT SELECT ON SYS.V_$ARCHIVE_DEST_STATUS TO username;
GRANT SELECT ON SYS.V_$LOGMNR_CONTENTS TO username;
GRANT SELECT ON DBA_TABLES TO username CONTAINER=ALL;
GRANT SELECT ON DBA_TAB_COLS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_CONSTRAINTS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_CONS_COLUMNS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_OBJECTS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_EXTENTS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_SEGMENTS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_LOBS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_TAB_SUBPARTITIONS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_TAB_PARTITIONS TO username CONTAINER=ALL;
GRANT EXECUTE ON DBMS_LOGMNR TO username;
GRANT SELECT ON V_$PARAMETER TO username CONTAINER=ALL;
GRANT SELECT ON DBA_LOB_PARTITIONS TO username CONTAINER=ALL;
GRANT SELECT ON DBA_LOB_SUBPARTITIONS TO username CONTAINER=ALL;
```

== METADATA

The METADATA mode requires the tool user to have the following privileges in the Oracle database: connection privilege, create table privilege, container switch privilege (when data is in PDB), privileges required to query database parameters, query schema existence privilege, query table existence privilege, privileges for column information views, privileges for primary key constraints, and privileges for constraint column names.

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required to query sequence information and perform flashback queries on sequence information.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required to query partition tables and perform flashback queries on partition tables.

```sql
-- When Oracle is in a non-container architecture, or when Oracle is in a container architecture but the data resides in the CDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;

-- When Oracle is in a container architecture and the data resides in a PDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SET CONTAINER TO dmc_user CONTAINER=ALL;
GRANT SELECT ON V_$DATABASE TO dmc_user CONTAINER=ALL;
GRANT SELECT ON V_$PARAMETER TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_USERS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_TABLES TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user CONTAINER=ALL;
-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user CONTAINER=ALL;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
```

== SNAPSHOT

The SNAPSHOT mode requires the tool user to have the following privileges in the Oracle database: connection privilege, create table privilege, privileges required to query container information, container switch privilege (when data is in PDB), privileges required to query database parameters, query schema existence privilege, query table existence privilege, query column information views, query primary key constraints, query constraint column names, query the size of tables to be synchronized, query large field information of tables to be synchronized, query the database segment extension information for concurrent table split synchronization, query database object information for concurrent table split synchronization, query partition table subpartition information (used for concurrent table split synchronization), query partition table partition information (used for concurrent table split synchronization), privileges required to query database parameters, and privileges required to create private temporary tables.

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required to query sequence information and perform flashback queries on sequence information.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required to query partition tables and perform flashback queries on partition tables.

```sql
-- When Oracle is in a non-container architecture, or when Oracle is in a container architecture but the data resides in the CDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT SELECT ON DBA_SEGMENTS TO dmc_user;
GRANT SELECT ON DBA_LOBS TO dmc_user;
GRANT SELECT ON DBA_EXTENTS TO dmc_user;
GRANT SELECT ON DBA_OBJECTS TO dmc_user;
GRANT SELECT ON DBA_TAB_SUBPARTITIONS TO dmc_user;
GRANT SELECT ON DBA_TAB_PARTITIONS TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT CREATE TABLE TO dmc_user;
-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;

-- When Oracle is in a container architecture and the data resides in a PDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SET CONTAINER TO dmc_user CONTAINER=ALL;
GRANT SELECT ON V_$DATABASE TO dmc_user CONTAINER=ALL;
GRANT SELECT ON V_$PARAMETER TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_USERS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_TABLES TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user CONTAINER=ALL;
GRANT SELECT ANY TABLE TO dmc_user CONTAINER=ALL
GRANT SELECT ON DBA_SEGMENTS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_LOBS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_EXTENTS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_OBJECTS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_TAB_SUBPARTITIONS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON DBA_TAB_PARTITIONS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON V_$PARAMETER TO dmc_user CONTAINER=ALL;
-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user CONTAINER=ALL;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
```

== INCREMENT

The INCREMENT mode requires the tool user to have the following privileges in the Oracle database: connection privilege, create table privilege, privileges required to query container information, container switch privilege (when data is in PDB), flashback query privileges to check if the schema exists, flashback query privileges to check if the table exists, flashback query privileges for column information views, flashback query privileges for primary key constraints, flashback query privileges for constraint column names, LogMiner and related view operation privileges, privilege for the connecting user to create tables, default tablespace privilege for the connecting user, privileges for user quotas on tablespace size, and privileges required to create LOG MINER heartbeat tables.

When the task configuration options->heartbeatEnabled= true, additional privileges are required for creating heartbeat tables, inserting and updating heartbeat tables, and for tablespace required for inserting data into the heartbeat tables.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required to query partition tables and perform flashback queries on partition tables.

```sql
-- When Oracle is in a non-container architecture, or when Oracle is in a container architecture but the data resides in the CDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT FLASHBACK ON DBA_USERS TO dmc_user;
GRANT FLASHBACK ON DBA_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_TAB_COLS TO dmc_user;
GRANT FLASHBACK ON DBA_CONSTRAINTS TO dmc_user;
GRANT FLASHBACK ON DBA_CONS_COLUMNS TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;
GRANT SELECT_CATALOG_ROLE TO dmc_user;
GRANT EXECUTE_CATALOG_ROLE TO dmc_user;
GRANT LOGMINING TO dmc_user;
GRANT CREATE TABLE TO dmc_user;
ALTER USER dmc_user QUOTA size ON USERS;
-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
-- When the task configuration options->heartbeatEnabled= true
GRANT CREATE ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;
ALTER USER dmc_user QUOTA UNLIMITED ON your_tablespacename;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;

-- When Oracle is in a container architecture and the data resides in a PDB, execute the following statements for authorization
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SET CONTAINER TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_USERS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_TABLES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_TAB_COLS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_CONSTRAINTS TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_CONS_COLUMNS TO dmc_user CONTAINER=ALL;
GRANT SELECT ON V_$DATABASE TO dmc_user CONTAINER=ALL;
GRANT SELECT_CATALOG_ROLE TO dmc_user CONTAINER=ALL;
GRANT EXECUTE_CATALOG_ROLE TO dmc_user CONTAINER=ALL;
GRANT LOGMINING TO dmc_user;
GRANT CREATE TABLE TO dmc_user;
ALTER USER dmc_user QUOTA size ON USERS;
-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user CONTAINER=ALL;
-- When the task configuration options->heartbeatEnabled= true
GRANT CREATE ANY TABLE TO dmc_user CONTAINER=ALL;
GRANT INSERT ANY TABLE TO dmc_user CONTAINER=ALL;
ALTER USER dmc_user QUOTA UNLIMITED ON your_tablespacename CONTAINER=ALL;
-- When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user CONTAINER=ALL;
```

:::

### Enabling Log Archiving

Incremental data synchronization requires Oracle database log archiving to be enabled.

1. Connect to the Oracle database as the DBA user:

   ```shell
   -- Note that CDB and Non-CDB may have different ORACLE_SID values
   $ export ORACLE_SID=SID
   $ sqlplus /nolog
     CONNECT sys/password AS SYSDBA
   ```

2. Enable log archiving and enable_goldengate_replication, ensuring that the /opt/oracle/oradata/recovery_area directory is created beforehand:

   ```sql
   ALTER SYSTEM SET db_recovery_file_dest_size = 10G;
   ALTER SYSTEM SET db_recovery_file_dest = '/opt/oracle/oradata/recovery_area' scope=spfile;
   ALTER SYSTEM SET enable_goldengate_replication=true;
   SHUTDOWN IMMEDIATE;
   startup mount;
   ALTER DATABASE ARCHIVELOG;
   ALTER DATABASE OPEN;
   ```

   > **Note**：
   >
   > Enabling log archiving requires a database restart, so please proceed with caution.
   > Archive log files can occupy a large amount of disk space, so consider periodically cleaning up expired logs.
   > The enable_goldengate_deplication configuration is designed to address the issue of data synchronization not being incrementally possible when the column name length exceeds 30.

3. Check if log archiving and enable_goldengate_deplication are enabled:

   ```sql
   archive log list;
   SELECT NAME, VALUE FROM V$PARAMETER WHERE NAME = 'enable_goldengate_replication'; 
   ```

### Enabling Supplemental Logging

Incremental data synchronization requires the Oracle database to enable supplemental logging. This can be done as follows(Full database additional logging is recommended as a priority):

```sql
-- Enable supplemental logging for the entire database
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;

-- Enable supplemental logging at the table level(Additional supplemental logging must be enabled for the heartbeat table $YDS_HEARTBEAT_YDS_INSTANCE and the flush table LOG_MINING_FLUSH.)
ALTER TABLE tablename ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
```

### Enabling Sequence Supplemental Logging

DMC requires sequence supplemental logging during sequence push HWM, which can be enabled using the following statement:

```sql
ALTER TABLE SYS.SEQ$ ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
```

## Target Environment Preparation

### YashanDB Authorization

According to different migration modes, execute the following corresponding statements in YashanDB for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

The INITIAL mode requires the tool user to have the following privileges in YashanDB: connection privilege, modify session parameter privilege, privileges required to query database parameters, create table privilege, query schema existence privilege, query table existence privilege, query column information views, query primary key constraints, create table privilege, create index privilege, delete or truncate table privilege (covering policy deletes the same table, truncating policy empties table data), query table privilege, insert table privilege, and query SCN at record refresh point privilege (when enabling record refresh point functionality).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required for creating and deleting sequences.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT CREATE ANY TABLE TO dmc_user;
GRANT CREATE ANY INDEX TO dmc_user;
GRANT DROP ANY TABLE TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;

-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

== METADATA

The METADATA mode requires the tool user to have the following privileges in YashanDB: connection privilege, modify session parameter privilege, privileges required to query database parameters, create table privilege, query schema existence privilege, query table existence privilege, query column information views, query primary key constraints, create table privilege, create index privilege, delete or truncate table privilege (covering policy deletes the same table, truncating policy empties table data).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required for creating and deleting sequences.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT CREATE ANY TABLE TO dmc_user;
GRANT CREATE ANY INDEX TO dmc_user;
GRANT DROP ANY TABLE TO dmc_user;

-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
GRANT SELECT ANY SEQUENCE TO dmc_user;
```

== SNAPSHOT

The SNAPSHOT mode requires the tool user to have the following privileges in YashanDB: connection privilege, modify session parameter privilege, privileges required to query database parameters, create table privilege, query schema existence privilege, query table existence privilege, query column information views, query primary key constraints, create table privilege, create index privilege, delete or truncate table privilege (covering policy deletes the same table, truncating policy empties table data), query table privilege, insert table privilege.

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required for creating and deleting sequences.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT CREATE ANY TABLE TO dmc_user;
GRANT CREATE ANY INDEX TO dmc_user;
GRANT DROP ANY TABLE TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;

-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

== INCREMENT

The INCREMENT mode requires the tool user to have the following privileges in YashanDB: connection privilege, modify session parameter privilege, privileges required to query database parameters (when the source is MySQL), create table privilege, query schema existence privilege, query table existence privilege, query column information views, query primary key constraints, create table privilege, create index privilege, delete or truncate table privilege (covering policy deletes the same table, truncating policy empties table data), privileges required to modify table structure and definitions and manage the table, query table privilege, insert table privilege, update table data privilege, delete table data privilege, and query SCN at record refresh point privilege (when enabling record refresh point functionality).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required for creating sequences.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT CREATE ANY TABLE TO dmc_user;
GRANT CREATE ANY INDEX TO dmc_user;
GRANT DROP ANY TABLE TO dmc_user;
GRANT ALTER ANY TABLE TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;
GRANT UPDATE ANY TABLE TO dmc_user;
GRANT DELETE ANY TABLE TO dmc_user;
GRANT SELECT ON V_$DATABASETO dmc_user;

-- When the task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

:::

### Database Parameter Configuration

During full data synchronization and incremental synchronization, if the configured DMC concurrency level is high, please adjust the corresponding database parameter WORK_AREA_POOL_SIZE reasonably; otherwise, it may lead to task failure.