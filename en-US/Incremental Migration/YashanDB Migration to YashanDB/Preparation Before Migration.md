## Source Environment Preparation

### YashanDB Authorization

Based on different migration modes, execute the following statements in YashanDB for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

The INITIAL mode requires the tool user to have the following privileges in YashanDB: create session privilege, alter session privilege, query active transactions privilege, query flashback scn privilege, query database parameters privilege, flashback query schema information privilege, flashback query table information privilege, flashback query column information privilege, flashback query primary key constraints privilege, query tables to be synchronized privilege, flashback query tables to be synchronized privilege, YStream usage privilege, query YStream views privilege, query table size of tables to be synchronized privilege, full data synchronization phase table partitioning privilege, query partition table LOB information (for concurrent table split synchronization), query subpartition LOB information of composite partition (for concurrent table split synchronization).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required: query sequence information privilege, flashback query sequence information privilege, query sequence information dependencies privilege, flashback query sequence information dependencies privilege.

When the task configuration options->heartbeatEnabled = true, additional privileges are required: create heartbeat table privilege, insert and update heartbeat table privilege.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required: query partition table privilege, flashback query partition table privilege.

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

-- When task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
GRANT SELECT ON DBA_DEPENDENCIES TO dmc_user;
GRANT FLASHBACK ON DBA_DEPENDENCIES TO dmc_user;

-- When task configuration options->heartbeatEnabled= true
GRANT CREATE ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;

-- When task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

== METADATA

The METADATA mode requires the tool user to have the following privileges in YashanDB: create session privilege, alter session privilege, query database parameters privilege.

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required: query sequence information privilege, flashback query sequence information privilege, query sequence information dependencies privilege, flashback query sequence information dependencies privilege.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required: query partition table privilege, flashback query partition table privilege.

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

-- When task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
GRANT SELECT ON DBA_DEPENDENCIES TO dmc_user;
GRANT FLASHBACK ON DBA_DEPENDENCIES TO dmc_user;

-- When task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

== SNAPSHOT

The SNAPSHOT mode requires the tool user to have the following privileges in YashanDB: create session privilege, alter session privilege, query flashback scn privilege, query database parameters privilege, query tables to be synchronized privilege, flashback query tables to be synchronized privilege, query the size of tables to be synchronized privilege, query the table size of tables to be synchronized privilege, full data synchronization phase table partitioning privilege, query partition table LOB information (for concurrent table split synchronization), query subpartition LOB information of composite partition (for concurrent table split synchronization).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required: query sequence information privilege, flashback query sequence information privilege, query sequence information dependencies privilege, flashback query sequence information dependencies privilege.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required: query partition table privilege, flashback query partition table privilege.

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

-- When task configuration options->sequenceSync = true or using the default configuration
GRANT SELECT ON DBA_SEQUENCES TO dmc_user;
GRANT FLASHBACK ON DBA_SEQUENCES TO dmc_user;
GRANT SELECT ON DBA_DEPENDENCIES TO dmc_user;
GRANT FLASHBACK ON DBA_DEPENDENCIES TO dmc_user;

-- When task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

== INCREMENT

The INCREMENT mode requires the tool user to have the following privileges in YashanDB: create session privilege, alter session privilege, query active transactions privilege, query flashback scn privilege, query database parameters privilege, flashback query schema information privilege, flashback query table information privilege, flashback query column information privilege, flashback query primary key constraints privilege, YStream usage privilege, query YStream views privilege.

When the task configuration options->heartbeatEnabled= true, additional privileges are required: create heartbeat table privilege, insert and update heartbeat table privilege.

When the task configuration options->partitionTableInterceptionEnabled = true or using the default configuration, additional privileges are required: query partition table privilege, flashback query partition table privilege.

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

-- When task configuration options->heartbeatEnabled= true
GRANT CREATE ANY TABLE TO dmc_user;
GRANT INSERT ANY TABLE TO dmc_user;

-- When task configuration options->partitionTableInterceptionEnabled = true or using the default configuration
GRANT SELECT ON DBA_PART_TABLES TO dmc_user;
GRANT FLASHBACK ON DBA_PART_TABLES TO dmc_user;
```

:::

### Database Parameter Adjustment

#### YStream Related Parameters

Incremental data relies on YStream to obtain committed data from YashanDB in real-time. The STREAM_POOL_SIZE parameter value must first be configured on YashanDB to allocate memory for YStream.

For large transactions, it is recommended to increase this parameter; otherwise, the task may fail.

The size of STREAM_POOL_SIZE exceeding SHARE_POOL_SIZE setting will be ineffective. If it exceeds, first increase the SHARE_POOL_SIZE parameter value.

#### Transaction Consistency Related Parameters

When the options.consistencyLevel parameter is set to transaction-consistent or transaction-serialize in the task configuration file, enabling transaction consistency synchronization, it is recommended to appropriately modify YashanDB's configuration to increase throughput. The modification statements are as follows:

```sql
ALTER SYSTEM SET COMMIT_LOGGING=BATCH scope = both;
ALTER SYSTEM SET _BATCH_COMMIT_DELAY=1000 scope = both;
```

When the COMMIT_LOGGING parameter is set to BATCH, the transaction is delayed for group submission when committed to the Redo log to improve transaction throughput. Transactions in group submission will be replayed concurrently during DMC replay. If concurrent replay is not desired, this parameter can be changed to IMMEDIATE for immediate submission.

The _BATCH_COMMIT_DELAY parameter can specify the delay wait time (in μs) when the transaction is committed to the Redo log. The default value is 0, which is adaptive. The more transactions in a batch, the more transactions can be replayed concurrently, improving DMC throughput.

> **Caution**：
>
> When the _BATCH_COMMIT_DELAY is set large, each transaction will delay longer during submission, accumulating more concurrent transactions for group submission. This will also increase DMC transaction concurrency, but it may affect the performance of the source database, so please set it carefully.

### Enable Supplemental Logging

Incremental data synchronization requires that Yashan Database enable supplemental logging. The method is as follows(Full database additional logging is recommended as a priority):

```sql
-- Enable supplemental logging for the whole database
ALTER DATABASE ADD SUPPLEMENTAL LOG TABLE TYPE (HEAP);
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;

-- Enable table-level supplemental logging
ALTER TABLE tablename ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
```

### Statistics Collection

To speed up metadata synchronization, it is recommended to collect statistics for the system tables before starting the migration task. The statement is as follows:

```sql
exec DBMS_STATS.GATHER_SCHEMA_STATS('SYS', 1, FALSE, 'FOR ALL COLUMNS SIZE auto', 4, 'AUTO', TRUE);
```

Currently, YashanDB does not support using indexes for flashback queries. If you have not completed the above steps, it may result in significantly slow metadata synchronization.

## Target Environment Preparation

### YashanDB Authorization

Based on different migration modes, execute the following statements in YashanDB for authorization(It is recommended to authorize in INITIAL mode by default):

::: tabs

== INITIAL

The INITIAL mode requires the tool user to have the following privileges in YashanDB: connect privilege, resource privilege, alter session privilege, query database parameters privilege, create table privilege, query schema existence privilege, query table existence privilege, query column information privilege, query primary key constraints privilege, create table privilege, create index privilege, drop or truncate table privilege (overwrite policy will drop the table with the same name, truncate policy will clear table data), query table privilege, insert table privilege, query SCN when refreshing records privilege (when enabling record refresh point functionality).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required: create sequence privilege, drop sequence privilege.

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

-- When task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

== METADATA

The METADATA mode requires the tool user to have the following privileges in YashanDB: connect privilege, resource privilege, alter session privilege, query database parameters privilege, create table privilege, query schema existence privilege, query table existence privilege, query column information privilege, query primary key constraints privilege, create table privilege, create index privilege, drop or truncate table privilege (overwrite policy will drop the table with the same name, truncate policy will clear table data).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required: create sequence privilege, drop sequence privilege.

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

-- When task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

== SNAPSHOT

The SNAPSHOT mode requires the tool user to have the following privileges in YashanDB: connect privilege, resource privilege, alter session privilege, query database parameters privilege, create table privilege, query schema existence privilege, query table existence privilege, query column information privilege, query primary key constraints privilege, create table privilege, create index privilege, drop or truncate table privilege (overwrite policy will drop the table with the same name, truncate policy will clear table data), query table privilege, insert table privilege.

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required: create sequence privilege, drop sequence privilege.

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

-- When task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

== INCREMENT

The INCREMENT mode requires the tool user to have the following privileges in YashanDB: connect privilege, resource privilege, alter session privilege, query database parameters privilege, create table privilege, query schema existence privilege, query table existence privilege, query column information privilege, query primary key constraints privilege, create table privilege, create index privilege, drop or truncate table privilege (overwrite policy will drop the table with the same name, truncate policy will clear table data), modify table structure and definitions, perform related management operations on tables, query table privilege, insert table privilege, update table data privilege, delete table data privilege, query SCN when refreshing records privilege (when enabling record refresh point functionality).

When the task configuration options->sequenceSync = true or using the default configuration, additional privileges are required: create sequence privilege, drop sequence privilege.

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

-- When task configuration options->sequenceSync = true or using the default configuration
GRANT CREATE ANY SEQUENCE TO dmc_user;
GRANT DROP ANY SEQUENCE TO dmc_user;
```

:::

### Database Parameter Configuration

During full data synchronization and incremental synchronization processes, if the configured DMC concurrency is high, please adjust the corresponding database parameter WORK_AREA_POOL_SIZE reasonably; otherwise, it may lead to task failures.