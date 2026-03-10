## Source Environment Preparation

### YashanDB Authorization

Depending on the migration mode, the following statements need to be executed in YashanDB for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

The INITIAL mode requires the tool user to have the following privileges: create session, modify session parameters, query active transactions, query flashback SCN, query database parameters, flashback query schema information, flashback query table information, flashback query column information, flashback query primary key constraints, query pending synchronization tables, flashback query pending synchronization tables, privileges for using YStream, query YStream views, query sizes of pending synchronization tables, privileges for splitting tables during the full data synchronization phase, query partition table LOB information (for concurrent table splitting synchronization), query composite partition's subpartition LOB information (for concurrent table splitting synchronization).

When the task configuration `options->sequenceSync = true` or using the default configuration, the following privileges are also required: query sequence information, flashback query sequence information, query table-dependent sequence information, flashback query table-dependent sequence information.

When the task configuration `options->heartbeatEnabled = true`, privileges for creating heartbeat tables, and inserting and updating heartbeat tables are also needed.

When the task configuration `options->partitionTableInterceptionEnabled = true` or using the default configuration, the following privileges are also required: query partition tables and flashback query partition tables.

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
GRANT SELECT ON DBA_TAB_SUBPARTITIONS TO dmc_user;
GRANT SELECT ON DBA_TAB_PARTITIONS TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_TAB_SUBPARTITIONS TO dmc_user;
GRANT SELECT ON DBA_LOB_PARTITIONS TO dmc_user;
GRANT SELECT ON DBA_LOB_SUBPARTITIONS TO dmc_user;

-- When the task configuration options->sequenceSync = true or using default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
GRANT SELECT ON DBA_DEPENDENCIES TO dmc_user;
GRANT FLASHBACK ON DBA_DEPENDENCIES TO dmc_user;

-- When the task configuration options->heartbeatEnabled= true
GRANT CREATE ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;

-- When the task configuration options->partitionTableInterceptionEnabled = true or using default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

== METADATA

The METADATA mode requires the tool user to have privileges to create sessions, modify session parameters, and query database parameters.

When the task configuration `options->sequenceSync = true` or using the default configuration, privileges for querying sequence information, flashback querying sequence information, querying table-dependent sequence information, and flashback querying table-dependent sequence information are also required.

When the task configuration `options->partitionTableInterceptionEnabled = true` or using the default configuration, privileges for querying partition tables and flashback querying partition tables are required.

```sql
GRANT CREATE SESSION TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;
GRANT SELECT ON V_$PARAMETER TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user; 
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;

-- When the task configuration options->sequenceSync = true or using default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
GRANT SELECT ON DBA_DEPENDENCIES TO dmc_user;
GRANT FLASHBACK ON DBA_DEPENDENCIES TO dmc_user;

-- When the task configuration options->partitionTableInterceptionEnabled = true or using default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

== SNAPSHOT

The SNAPSHOT mode requires the tool user to have privileges to create sessions, modify session parameters, query flashback SCN, query database parameters, query pending synchronization tables, flashback query pending synchronization tables, query sizes of pending synchronization tables, query sizes of tables during full data synchronization, privileges for splitting tables during full data synchronization, query partition table LOB information (for concurrent table splitting synchronization), and query composite partition's subpartition LOB information (for concurrent table splitting synchronization).

When the task configuration `options->sequenceSync = true` or using the default configuration, the privileges for querying sequence info, flashback querying sequence info, querying table-dependent sequence info, and flashback querying table-dependent sequence info are also required.

When the task configuration `options->partitionTableInterceptionEnabled = true` or using the default configuration, the privileges for querying partition tables and flashback querying partition tables are also required.

```sql
GRANT CREATE SESSION TO dmc_user;
GRANT ALTER SESSION TO dmc_user;
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
GRANT SELECT ON V_$DATAFILE TO dmc_user;
GRANT SELECT ON DBA_TAB_SUBPARTITIONS TO dmc_user;
GRANT SELECT ON DBA_TAB_PARTITIONS TO dmc_user;
GRANT SELECT ON DBA_LOB_PARTITIONS TO dmc_user;
GRANT SELECT ON DBA_LOB_SUBPARTITIONS TO dmc_user;

-- When the task configuration options->sequenceSync = true or using default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
GRANT SELECT ON DBA_DEPENDENCIES TO dmc_user;
GRANT FLASHBACK ON DBA_DEPENDENCIES TO dmc_user;

-- When the task configuration options->partitionTableInterceptionEnabled = true or using default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

== INCREMENT

The INCREMENT mode requires the tool user to have privileges to create sessions, modify session parameters, query active transactions, query flashback SCN, query database parameters, flashback query schema information, flashback query table information, flashback query column information, flashback query primary key constraints, privileges for using YStream, and privileges for querying YStream views.

When the task configuration `options->heartbeatEnabled = true`, privileges for creating heartbeat tables, and inserting and updating heartbeat tables are also needed.

When the task configuration `options->partitionTableInterceptionEnabled = true` or using the default configuration, privileges for querying partition tables and flashback querying partition tables are also needed.

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

-- When the task configuration options->heartbeatEnabled = true
GRANT CREATE ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;

-- When the task configuration options->partitionTableInterceptionEnabled = true or using default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

:::

### Database Parameter Adjustment

##### YStream Related Parameters

Incremental data relies on YStream to acquire committed data in real-time from YashanDB, so the `STREAM_POOL_SIZE` parameter must be configured in YashanDB to allocate memory for YStream.

For large transactions, it is recommended to increase this parameter, otherwise, the task may fail.

If the size of `STREAM_POOL_SIZE` exceeds the `SHARE_POOL_SIZE` setting, it will become invalid, requiring an increase in the `SHARE_POOL_SIZE` parameter first.

##### Transaction Consistency Related Parameters

When the task configuration file sets `options.consistencyLevel` to `transaction-consistent` or `transaction-serialize`, enabling transaction consistency synchronization, it is recommended to adjust the YashanDB configuration to increase throughput. The modification statements are as follows:

```sql
ALTER SYSTEM SET COMMIT_LOGGING=BATCH scope = both;
ALTER SYSTEM SET _BATCH_COMMIT_DELAY=1000 scope = both;
```

When `COMMIT_LOGGING` parameter is set to BATCH, the transaction commit to the redo log will be delayed for batch commits to increase transaction throughput. Transactions in the group commit will be replayed concurrently during DMC replay; if concurrent replay is not desired, this parameter can be changed to IMMEDIATE for immediate commit.

The `_BATCH_COMMIT_DELAY` parameter specifies the delay waiting time (in μs) for transactions to commit to the redo log. The default value is 0, which is adaptive. The more transactions in a batch, the more transactions can be concurrently replayed to improve DMC throughput.

> **Caution**：
>
> When `_BATCH_COMMIT_DELAY` is set larger, each transaction commit will be delayed longer, accumulating more concurrent transactions for batch commit. This will increase DMC transaction concurrency but may impact the performance of the source database, so please be cautious when setting it.

### Enable Supplemental Logging

Incremental data synchronization requires Yashan Database to enable supplemental logging as follows(Full database additional logging is recommended as a priority):

```sql
-- Enable supplemental logging for the entire database
ALTER DATABASE ADD SUPPLEMENTAL LOG TABLE TYPE (HEAP);
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;

-- Enable supplemental logging at the table level
ALTER TABLE tablename ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
```

### Statistics Collection

To speed up metadata synchronization, it is recommended to collect statistics of system tables before starting the migration task, as follows:

```sql
exec DBMS_STATS.GATHER_SCHEMA_STATS('SYS', 1, FALSE, 'FOR ALL COLUMNS SIZE auto', 4, 'AUTO', TRUE);
```

The current version of YashanDB does not support index usage for flashback query; if you do not complete the above steps, it may lead to abnormally slow metadata synchronization.

## Target Environment Preparation

### Oracle Authorization

Depending on the migration mode, the following statements need to be executed in Oracle for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

The INITIAL mode requires the tool user to have the following privileges: connect privileges for the Oracle database, privileges to create and manage database objects, unlimited tablespace usage privileges, query privileges for user information, table information, column information, query primary key constraints, query constraints column information, create table privileges, create index privileges, modify table privileges, delete or truncate table privileges (override strategy will delete tables with the same name, truncate strategy will empty table data), delete index privileges, insert table privileges, update table data privileges, delete table data privileges, and query SCN during record refresh point (when enabling the record refresh point functionality).

When the task configuration `options->sequenceSync = true` or using the default configuration, the privileges to create and delete sequences are also required.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT CREATE ANY TABLE TO dmc_user;
GRANT CREATE ANY INDEX TO dmc_user;
GRANT ALTER ANY TABLE TO dmc_user;
GRANT DROP ANY TABLE TO dmc_user;
GRANT DROP ANY INDEX TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;
GRANT UPDATE ANY TABLE TO dmc_user;
GRANT DELETE ANY TABLE TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;

-- When the task configuration options->sequenceSync = true or using default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

== METADATA

The METADATA mode requires the tool user to have the following privileges: connect privileges for the Oracle database, privileges to create and manage database objects, unlimited tablespace usage privileges, query privileges for user information, table information, column information, query primary key constraints, query constraints column information, create table privileges, create index privileges, delete or truncate table privileges.

When the task configuration `options->sequenceSync = true` or using the default configuration, privileges for creating and deleting sequences are also required.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT CREATE ANY TABLE TO dmc_user;
GRANT CREATE ANY INDEX TO dmc_user;
GRANT DROP ANY TABLE TO dmc_user;

-- When the task configuration options->sequenceSync = true or using default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

== SNAPSHOT

The SNAPSHOT mode requires the tool user to have connect privileges for the Oracle database, privileges to create and manage database objects, unlimited tablespace usage privileges, query privileges for user information, table information, column information, query primary key constraints, query constraints column information, create table privileges, create index privileges, delete or truncate table privileges, query privileges on tables, insert privileges on tables.

When the task configuration `options->sequenceSync = true` or using the default configuration, privileges for creating and deleting sequences are also required.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
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

The INCREMENT mode requires the tool user to have connect privileges for the Oracle database, privileges to create and manage database objects, unlimited tablespace usage privileges, query privileges for user information, table information, column information, query primary key constraints, query constraints column information, create table privileges, create index privileges, modify table privileges, delete or truncate table privileges, delete index privileges, query table data privileges, insert table data privileges, update table data privileges, delete table data privileges, and query SCN during record refresh point (when enabling record refresh point functionality).

When the task configuration `options->sequenceSync = true` or using the default configuration, additional privileges for creating, deleting, and querying sequence information are also required.

```sql
GRANT CONNECT TO dmc_user;
GRANT RESOURCE TO dmc_user;
GRANT UNLIMITED TABLESPACE TO dmc_user;
GRANT SELECT ON DBA_USERS TO dmc_user;
GRANT SELECT ON DBA_TABLES TO dmc_user;
GRANT SELECT ON DBA_TAB_COLS TO dmc_user;
GRANT SELECT ON DBA_CONSTRAINTS TO dmc_user;
GRANT SELECT ON DBA_CONS_COLUMNS TO dmc_user;
GRANT CREATE ANY TABLE TO dmc_user;
GRANT CREATE ANY INDEX TO dmc_user;
GRANT ALTER ANY TABLE TO dmc_user
GRANT DROP ANY TABLE TO dmc_user;
GRANT DROP ANY INDEX TO dmc_user;
GRANT SELECT ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;
GRANT UPDATE ANY TABLE TO dmc_user;
GRANT DELETE ANY TABLE TO dmc_user;
GRANT SELECT ON V_$DATABASE TO dmc_user;

-- When the task configuration options->sequenceSync = true or using default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
GRANT SELECT ANY SEQUENCE TO dmc_user;
```

:::

> **Caution**:
>
> If the Oracle users within the synchronization scope do not have tablespace privileges, errors such as `ORA-01950: insufficient privileges on tablespace 'USERS'` may occur during full or incremental data synchronization. Please grant the corresponding tablespace privileges to these users before starting the synchronization task.

### Enable Extended Length

The maximum length supported by the variable-length string type in Oracle Database is 4000 characters by default. If the length supported by the source side exceeds 4000 characters, synchronization may fail. Please consider enabling extended length (EXTENDED mode) based on the actual situation.