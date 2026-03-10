Before starting the migration task, a series of task parameter configurations must be completed, and DMC configures these by directly editing the task configuration file. The default task configuration file is located at task_config.yml in the conf directory.

All parameters supported by DMC and their configuration methods are listed in the [Configuration File Introduction](../DMC Introduction/Configuration Files) document.

### Basic Parameter Configuration

Basic parameter configuration involves making some necessary settings to meet the requirements for successfully starting the task. As shown below:

```yaml
taskName: simple_task     # Change to the actual task name
source:
  type: mysql             # The source type must be mysql
  dataSource:    
    hostname:
      - host: 127.0.0.1    # Change to the actual MySQL server address
        port: 3306         # Change to the actual MySQL listening port
    username: dmc_user     # Change to the actual tool username
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==           # Fill in the string encrypted by encryptpwd
  options:
    connectorParameter:
      serverId: 123       # The serverId for the MySQL binlog connector; must be a different value than other tasks

sink:
  type: YashanDBMySQLMode   # The target type must be YashanDBMySQLMode
  dataSource:
    hostname:
      - host: 127.0.0.1     # Change to the actual YashanDB server address
        port: 1688          # Change to the actual YashanDB listening port
    username: dmc_user      # Change to the actual tool username    
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==           # Fill in the string encrypted by encryptpwd

options:
  syncMode: initial              # Change to the actual sync mode
  conflictStrategy: override     # Change to the actual conflict strategy
syncRange:
  schemas:
    - schema: YDS                # Change to the actual schema name
```

Among them, the database connection passwords for both the source and target need to be manually filled in after encryption using the DMC client, as follows:

```shell
$ cd ~/yas-data-sync/bin

$ ./start_client.sh
Welcome to the YAS-DATA-SYNC client!

Type 'help' for a list of commands or 'quit' to exit.

Copyright (c) 2024, YashanDB Development Group.
See the LICENSE file in the project root for more information.

YDS@8100> encryptpwd
Enter your password: ******
Your encrypted password:
HwfydolzxogxdRxTEmRQyrEq54NceA==
```

### Advanced Parameter Configuration

DMC provides a large number of parameters for user settings. Aside from the necessary parameter configurations mentioned above, other parameters are provided with default values to simplify usage.

When partitionTableInterceptionEnabled is set to false, the partition table will be synchronized as a normal table.

#### Connector Parameters

Connector parameters provide some optimization configuration options for connecting the source and target. Users can set these according to their needs or use default values.

Detailed descriptions of the MySQL and YashanDB connector parameters can be found in the [Source Connector Parameter Introduction](../DMC Introduction/Configuration Files.html#connectorparametersource) and [Target Connector Parameter Introduction](../DMC Introduction/Configuration Files.html#connectorparametertarget).

##### Specify the Sync Starting Point

This indicates the specification for obtaining the binary data file name and the starting position to read the binary data. Incremental data will be obtained from this starting point.

The following business scenarios require specifying the sync starting point:

- When migrating data and the production environment is busy, to reduce the pressure on the production environment, perform only a full sync first, record the incremental starting point after the full sync is complete, and then specify the sync starting point for incremental sync when the pressure on the production environment is lighter.
- To sync only the data generated during a specific time period, there is no need to start DMC in advance. Instead, record the starting point under a specific time period, and when the data is needed, specify the starting point to initiate DMC incremental sync.

The starting position for obtaining incremental data from MySQL is composed of binlogFileName and startPosition, which can be queried using the following SQL statement to obtain the current binlog file name and position of the MySQL database.

```sql
SHOW MASTER STATUS；
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000006 |      120 |              |                  |
+------------------+----------+--------------+------------------+
```

Fill in the File field from the result set to binlogFileName and the Position field to startPosition, then start DMC to begin syncing from that point.

```yaml
source：
  type: MySQL                                      
  dataSource:
    hostname:
      - host: 127.0.0.1                               
        port: 1688                                   
    username: sys                              
    password: V1SyJt4acNihc54MLSsUEb3J1F2FT41grw==        
  options:
	binlogFileName: mysql-bin.000006
	startPosition: 120
```

> **Warn**:
>
> Specifying a non-existent or incorrect binlog log position will result in a sync failure.

#### Hidden Parameters

In YashanDB, schema and table names are stored in lowercase, while column names are stored in uppercase. To ensure the completion of data synchronization and data validation, the hidden parameter options._caseSensitive._object should be configured to lower, and options._caseSensitive._column should be configured to upper.



#### Sync Range Parameters

All configuration options for the sync range are located under the [syncRange field](../DMC Introduction/Configuration Files.html#syncRangeParameter) in the task configuration file, classified into whitelist and blacklist. The whitelist configures the sync range of source schemas/tables and their mapping relationships to the target tables, while the blacklist configures the tables to be ignored on the source side. DDL and DML generated by such tables will not be synced to the target.

Please note the following configuration principles:

- It is not allowed to set the blacklist columns for tables with primary keys to all primary keys, which will cause sync errors.
- It is not allowed to rename a column in the blacklist to another name during the sync process. If misoperation occurs, please fix the metadata on the target side (by adding this column) before resuming the task.
- It is not allowed to rename a column with another name to a blacklist column during the sync process. If misoperation occurs, please fix the metadata on the target side (by removing this column) before resuming the task.
- If all columns of a table are in the blacklist, DMC will not perform the sync.
- For adding, deleting, or modifying columns, and adding primary keys, DMC will delete columns in the blacklist and proceed with the sync. If a statement deletes a column in the blacklist, it will have no effect on the target database, as DMC will ignore this DDL execution.
- If an ignored column is of a type that DMC cannot support (e.g., UDT), even if ignored, DMC will not support syncing that table.

##### Whitelist Configuration

Whitelist configuration is located under the `syncRange` field in the `schemas` field. Here are a few typical configuration examples:

::: tabs

== Configure Sync Range

**Sync Whole Schema**

For example, if the task needs to sync all tables under schemas `yds` and `YDS`, the whitelist can be configured as follows:

```yaml
syncRange:
  schemas:
    - schema: yds
    - schema: YDS
```

**Sync Specific Tables Under a Schema**

The `schema` and `tables` fields can be combined to sync specific tables under a schema. The `tables` field can configure multiple tables organized in an array format.

For example, if the task needs to sync tables `yds.table`, `yds.table1`, `YDS.TABLE`, `YDS.TABLE1`, the whitelist can be configured as follows:

```yaml
syncRange:
  schemas:
    - schema: yds
      tables: 
        - table: table
        - table: table1
    - schema: YDS
      tables:
        - table: TABLE
        - table: TABLE1
```

== Configure Mapping Relationships

**Configure Mapping Relationships for an Entire Schema**

The `mapName` field at the same level as the `schema` field is used to map all tables under the source schema to the specified target schema.

For example, if the task needs to sync all tables under schemas `yds` and `YDS` to target schemas `t_yds` and `T_YDS` respectively, the whitelist can be configured as follows:

```yaml
syncRange:
  schemas:
    - schema: yds
      mapName: t_yds
    - schema: YDS
      mapName: T_YDS
```

**Configure Mapping Relationships for Specific Tables Under a Schema**

The `mapSchema` and `mapName` fields at the same level as the `table` field are used to map specific tables under the source schema to the specified target schema and table.

For example, if the task needs to map the source tables `yds.table`, `YDS.TABLE`, and `YDS.TABLE1` to target tables `t_yds.t_table`, `T_YDS.T_TABLE`, and `T_YDS.T_TABLE1`, the whitelist can be configured as follows:

```yaml
syncRange:
  schemas:
    - schema: yds
      tables: 
        - table: table
          mapSchema: t_yds
          mapName: t_table
    - schema: YDS
      mapName: T_YDS
      tables:
        - table: TABLE
          mapName: T_TABLE
        - table: TABLE1
          mapName: T_TABLE1
```

== Configure Ignored Columns

Some business scenarios may require configuring ignored columns:

- It is not necessary to completely sync the source database data to the target database, only part of the data is needed.
- Due to business requirements, columns need to be renamed differently in the source and target databases during sync.

You can configure global ignored columns through the `options.syncIgnoreColumn` parameter, or configure table-level ignored columns through parameters under `syncRange`. When both are configured, the task will prioritize the table-level ignored columns.

```yaml
# Configuring the following content in the task configuration file will cause the task to ignore columns named ID and INSTANCE_ID for the TEST_SCHEMA.TEST table during sync
syncRange:
  schemas:
    - schema: TEST_SCHEMA
      tables:
        - table: TEST
          syncIgnoreColumn:
            - ID
            - INSTANCE_ID
            
# Configuring the following content in the task configuration file will cause the task to globally ignore columns named ID and INSTANCE_ID during sync
options:
  syncIgnoreColumn:
    - ID
    - INSTANCE_ID
```

:::

##### Blacklist Configuration

Blacklist configuration is located under the `syncRange` field in the `tablesBlackList` field, used to configure which tables will be ignored by the task, organized in an array format.

The declaration format of tables in the blacklist is `'"schema"."table"'`, where both schema and table are surrounded by double quotes and the whole is surrounded by single quotes.

For example, if the task needs to ignore tables `yds.ignore_table` and `YDS.IGNORE_TABLE`, the blacklist can be configured as follows:

```yaml
syncRange:
  tablesBlackList:
    - '"yds"."ignore_table"'
    - '"YDS"."IGNORE_TABLE"'
```

#### Sequence Sync Range Parameters

All configuration options for sequence sync range are located under the [sequenceSyncRange field](../DMC Introduction/Configuration Files.html#sequenceSyncRangeParameter) in the task configuration file, classified into whitelist and blacklist. The whitelist configures the sync range of source schemas/Sequences and their mapping relationships to the target Sequences, while the blacklist configures the sequences to be ignored on the source side.

The `sequenceSyncRange` field should be used in conjunction with the `syncRange` field, with the following rules:

- If `syncRange` is only configured with schemas, all sequences under those schemas will be synced.
- If both `syncRange` and `sequenceSyncRange` are configured with schema mappings, the mapping in `sequenceSyncRange` takes precedence.
- If `syncRange` has table blacklists, sequences dependent on the blacklisted tables will sync if in the `sequenceSyncRange` whitelist.
- If `syncRange` has table whitelists, sequences that depend on those tables will not sync if in the `sequenceSyncRange` blacklist, and subsequent DDL and water level maintenance will not occur for those sequences.

#### Custom Data Type Mapping

DMC supports users in customizing conversion rules from tool types to target data types. Please refer to the [Configuration File Introduction](../DMC Introduction/Configuration Files.html#dataTypeCustomMapping) for configuration methods.

The mapping's impact range includes table creation during the metadata stage, new column types during the data DDL stage, and modifications to column types.



#### Lock Synchronization

DMC supports two full incremental sync modes: **Lock Synchronization** and **No Lock Synchronization**.

- In lock synchronization mode, DMC will preallocate some connections for lock synchronization during task initialization. The number of concurrent connections is related to the metadata sync source parallelism `source.options.metadataParallelism` and the full sync parallelism `options.snapshotParallelism`, calculated as follows: the number of connections = `max(source.options.metadataParallelism, options.snapshotParallelism * 2) + 2`.
- During the execution of lock synchronization, do not perform DDL operations on the objects within the migration scope before completing the full sync phase, as this may lead to task failure. Specific analyses are as follows:
  - Executing DDL operations during the task initialization phase or metadata synchronization phase may result in metadata validation failures, data sync failures, or incremental sync failures.
  - Executing DDL operations during the full sync phase, if CREATE TABLE is performed, will not affect the task; otherwise, it will lead to data sync failure.
- In no lock synchronization mode, DMC cannot guarantee data consistency, and users must ensure that no business data is written and no DDL changes occur before the DMC task enters the incremental sync phase.
- **DMC strongly recommends using lock synchronization mode**.



#### Incremental DDL Fault Tolerance Parameter

During incremental data sync, when captured DDL contains specification limitations, unsupported data types, or default values, the task will report an error and fail, and cannot be recovered.

By setting the task parameter `options.ddlFaultTolerance` to `ignore_table_all_operations`, this type of DDL can be ignored during the incremental data sync, and all subsequent DDL (except DROP TABLE) and DML involving those ignored tables will also be ignored, ensuring the task continues to run without being affected by those tables.

When an incremental phase captures a DDL of a MODIFY column or when unsupported data types exist, with fault tolerance enabled, the DDL will not cause the task to fail, it will not execute on the target side, and subsequent DDL and DML of the table `YDS.NOT_SUPPORTED_TABLE` will not execute on the target side.

#### Full Fault Tolerance Parameter

Full fault tolerance only takes effect in snapshot mode and is divided into fault tolerance for the metadata sync stage and the full sync stage. During the metadata sync stage, exceptions in the sync and verification between different tables will not affect each other. In the full sync stage, there are table inter-fault tolerance and table content errors. Table inter-fault tolerance indicates that sync errors within tables will not impact the normal sync of other tables. Table content errors indicate that sync errors during data insertion will only stop the sync of that table if the number of error rows exceeds the fault tolerance threshold.

- Setting `options.snapshotDmlFaultTolerance.enabled` to `true` in the task configuration file will activate fault tolerance during the metadata sync stage. Sync and verification of table metadata will not impact other tables; inter-table fault tolerance during the full sync stage will be enabled, and errors in the full sync will not affect the normal sync of other tables.
- Setting `options.snapshotDmlFaultTolerance.needFaultTolerant` to `true` in the task configuration file will enable content-level errors, which only takes effect when `options.snapshotDmlFaultTolerance.enabled` is `true`. If the number of error rows during the full data sync within a table exceeds the fault tolerance threshold (`options.snapshotDmlFaultTolerance.errorLimit`), the data sync task for that table will stop running, but will not affect the full data sync of other tables.
- Setting `options.snapshotDmlFaultTolerance.errorLimit` in the task configuration file to the fault tolerance threshold will activate the tolerance threshold for the number of sync errors within the table. This only takes effect when `options.snapshotDmlFaultTolerance.enabled` is `true` and `options.snapshotDmlFaultTolerance.needFaultTolerant` is `true`. If less than 0, there is no upper limit to tolerated errors; if equal to 0, the table's sync will stop immediately upon encountering an error; if greater than 0, the table will only stop syncing when the number of error rows exceeds this value.

#### Performance Parameters

By reasonably configuring certain parameters, resource utilization can be optimized, overall efficiency improved, and the system can quickly and stably process tasks while meeting requirements.

##### Concurrency Setting

1. The value of `source.options.metadataParallelism` in the task configuration file specifies how many threads the source connector will start to query metadata simultaneously. Each thread will hold one connection to the source database. When the number of tables to be synced is large (e.g., tens of thousands), increasing this value can speed up metadata querying.
2. The value of `sink.options.metadataParallelism` in the task configuration file specifies how many threads the target connector will open to execute DDL (e.g., create tables) simultaneously. Each thread will hold one connection to the target database. When the number of tables to be synced is large (e.g., tens of thousands), increasing this value can accelerate DDL execution.
3. The value of `options.snapshotParallelism` in the task configuration file specifies the concurrency for full data sync. As an I/O-intensive task, it is recommended to adjust this value based on the hardware specifications of the server and the load conditions of both connectors. It is recommended that each concurrent thread occupy one CPU logical core to ensure optimal performance.
4. The value of `sink.options.incrementParallelism` in the task configuration file specifies the maximum concurrency available for the target connector during DML execution, with different concurrency principles in data consistency mode and transaction consistency mode. Adjusting this value appropriately will significantly improve the performance of incremental data sync.

> **Note**:
>
> When adjusting the concurrency for full sync, please also adjust the heap size of DMC service to avoid task failure due to insufficient memory.

##### JVM Startup Parameters

Opening JVM startup parameter tuning can customize the JVM runtime environment for different scenarios and needs, maximizing performance, improving resource utilization, and ensuring the application's stability under high load while reducing performance fluctuations related to GC.

1. How to choose the appropriate garbage collector?
   - Based on data measured with the TPC-H model, when **full sync concurrency is less than 32** and **the average maximum memory size allocated to each concurrent thread is less than 2GB**, DMC recommends using ZGC as the garbage collector; otherwise, G1 is recommended.

2. What is the basis for configuring the JVM heap memory size?
   - First, please ensure that the average maximum memory size allocated to each concurrent thread is not less than 512MB.
   - Second, DMC recommends in production environments that the initial heap size (Xms) and maximum heap size (Xmx) of the service should be set to the same value to ensure stable operation under high load.

##### Cursor

A cursor refers to the cursor resources of the connector. DMC uses the LRU algorithm to buffer recently used cursor resources. Holding more cursors means that DMC can utilize buffered cursors to execute DML more quickly.

The `sink.options.incrementMaxUseCursors` in the task configuration file limits the maximum number of cursors that the target connector can use during incremental sync. Increasing this value can increase the number of buffered cursors in DMC, and adjusting this value appropriately will significantly enhance the performance of incremental data sync.

If this value is not set properly, it can lead to task failures due to insufficient resources during the sync.

##### Data Consistency Mode

When the value of `options.consistencyLevel` is set to `data-consistent` in the task configuration file, data consistency mode is enabled during sync.

The principle is to split the transaction information received by the source connector, using table names for hash mapping, allocating DML to each concurrently executing thread, which is beneficial for processing multi-table sync.

Data consistency mode also employs DML merging and batch insert techniques, suitable for sync tasks in analytic scenarios and when there are large transactions in the source end business.

##### Transaction Consistency Mode

When the value of `options.consistencyLevel` is set to `transaction-consistent` or `transaction-serialize` in the task configuration file, transaction consistency mode is enabled during sync.

The principle is based on the group commit technique used in writing logs by the source end business, naturally distinguishing unrelated transactions, allowing DMC to perform concurrent processing of these unrelated transactions during execution.

In this mode, DMC's throughput is related to the concurrency of the source end business, as well as some advanced parameters.

It should be noted that when the value of `options.consistencyLevel` is set to `transaction-serialize` in the task configuration file, DMC will still use this group commit technique, ensuring strict serial commit only before committing transactions.

##### Database Connection Pool

When the concurrency is high for full and incremental syncs, it is recommended to set the minimum connection configuration for the database connection pool to match the concurrency size, which helps improve sync performance.

```yaml
options:
  snapshotParallelism: 30
source:
  dataSource:
    pool:
      minimumIdle: 30
sink:
  dataSource:
    pool:
      minimumIdle: 30
```



#### Data Type Compatibility Parameters

Data type compatibility configurations are for automatic table creation in the forward link. If the metadata coverage policy for the reverse link is set to ignore mode, failures may occur due to data type verification.

Configuring data type compatibility may lead to data sync overflow, data truncation, and integrity risks; please use with caution.

|Data Type Compatibility Configuration |MySQL Data Type |YashanDB (MySQL Mode) Compatible Type |
| -------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| options.dataCompatibility. allNumericTypeCompatible=true | TINYINT、SMALLINT、INT、BIGINT、NUMBER(P, S)、FLOAT、DOUBLE、FLOAT UNSIGNED、DOUBLE UNSIGNED | TINYINT、SMALLINT、INT、BIGINT、NUMBER(P, S)、FLOAT、DOUBLE、FLOAT UNSIGNED、DOUBLE UNSIGNED |
| options.dataCompatibility. validateStringLength=false    | VARCHAR(size)                                                | VARCHAR(any size)                                            |
| options.dataCompatibility. validateEnumMapVarchar=true   | ENUM                                                         | VARCHAR(size)                                                |
| options.dataCompatibility. validateSetMapVarchar=true    | SET                                                          | VARCHAR(size)                                                |

- The TIMESTAMP type in YashanDB (MySQL mode) contains time information, so the synchronized data includes hours, minutes, and seconds, with the filling rules guaranteed by the database.

- The query result for MySQL Timestamp type is affected by the time zone. When migrating this type, please perform the following two steps to ensure that the sync results meet expectations:
  1. Set the source session parameter `source.options.connectorParameter.timezone` to the desired target time zone, with the default time zone being GMT+8:00.
  2. Set the source JDBC parameter `source.dataSource.properties.serverTimezone` to the local time zone. If this parameter is not set, it defaults to the JVM time zone (the specific behavior of the default value is guaranteed by MySQL JDBC).
