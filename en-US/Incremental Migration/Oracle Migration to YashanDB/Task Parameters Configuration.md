Before starting the migration task, a series of task parameter configurations must be completed first. DMC configures the parameters by directly editing the task configuration file. The default task configuration file is the task_config.yml file located in the conf directory.

All parameters supported by DMC and their configuration methods are listed in the [Configuration File Introduction](../DMC Introduction/Configuration Files) document.

### Basic Parameter Configuration

Basic parameter configuration involves making some necessary settings to meet the requirements for the task to start successfully. As shown below:

```yaml
taskName: simple_task     # Change to the actual task name
source:
  type: oracle             # The source type must be oracle
  dataSource:    
    hostname:
      - host: 127.0.0.1    # Change to the actual Oracle server address
        port: 1688         # Change to the actual Oracle listener port
    username: dmc_user     # Change to the actual tool username
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==           # Fill in the string encrypted by encryptpwd
    database: ORCLCDB          # Oracle database service name,specify the CDB name in multi-tenant mode
    pdbName: ORCLPDB1       # (Optional)Multi-tenant PDB Name. Automatically connect to the database location if left blank.
    options:
        connectorParameter:
    

sink:
  type: YashanDB            # The target type must be YashanDB
  dataSource:
    hostname:
      - host: 127.0.0.1     # Change to the actual YashanDB server address
        port: 1688          # Change to the actual YashanDB listener port
    username: dmc_user      # Change to the actual tool username   
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==           # Fill in the string encrypted by encryptpwd
    database: cdb$root      # YashanDB Service Name,The root or PDB name can be specified in multi-tenant mode; the tenant name of pdbName will be used if left blank.
    pdbName: PDB1           # Multi-tenant PDB Name,if the database is specified as the root,the system will connect to the root first.

options:
  syncMode: initial              # Change to the actual sync mode
  conflictStrategy: override     # Change to the actual conflict strategy
syncRange:
  schemas:
    - schema: YDS                # Change to the actual Schema name
```

The database connection passwords for the source and target must be manually filled in after encryption using the DMC client, as follows:

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

DMC provides a large number of parameters for users to set. In addition to the necessary parameters mentioned above, other parameters have default values to simplify usage.

#### Connector Parameters

Connector parameters provide some optimization configuration options for connecting to the source and target ends. Users can set these parameters according to their own situation or use the default values.

Detailed explanations of the connector parameters for the source and target can be found in the [Source Connector Parameter Introduction](../DMC Introduction/Configuration Files.html#connectorparametersource) and [Target Connector Parameter Introduction](../DMC Introduction/Configuration Files.html#connectorparametertarget).

##### Specify the Synchronization Starting Point

Indicates the filename for binary data and the starting position for reading binary data; incremental data is retrieved from this starting position.

The following business scenarios require specifying the synchronization starting point:

- When migrating data and the production environment is busy, to reduce pressure on the production system, you can first perform only a full sync. After the full sync is complete, record the incremental starting point. Then, when the production environment load is lighter, specify the synchronization starting point to perform incremental sync and complete the data synchronization.
- Synchronize data generated within a specific time period — no need to start DMC in advance. Instead, record the starting point for that time period beforehand, and when needed, start DMC incremental synchronization by specifying the recorded starting point.

The starting point for Oracle to retrieve incremental data consists solely of the startScn. You can query the current SCN of the database using the following SQL statement:

```sql
SELECT CURRENT_SCN FROM V$DATABASE;
+--------------------+
|   CURRENT_SCN      |
+--------------------+
| 157983196 |
+--------------------+
```

Query the current oldest active transaction (there may be no active transactions).

```sql
SELECT START_SCN AS START_POINT  FROM SYS.GV_$TRANSACTION ORDER BY START_SCN FETCH FIRST 1 ROWS ONLY;
+--------------------+
|   START_POINT      |
+--------------------+
| 157981184 |
+--------------------+
```

If there is no oldest active transaction, fill the CURRENT_SCN value from the result set into startScn, and fill startRestartScn with the value of CURRENT_SCN - 1. Synchronization can then begin from this point.

```yaml
source：
  type: Oracle                                      
  dataSource:
    database: ORCLCDB
    pdbName: ORCLPDB1
    hostname:
      - host: 127.0.0.1                               
        port: 1521  
    linkType: serviceName
    username: system                              
    password: V1SyJt4acNihc54MLSsUEb3J1F2FT41grw==        
  options:
    connectorParameter:
	  startScn: 157983196
	  startRestartScn: 157983195
```

If there is an oldest active transaction, fill the CURRENT_SCN value from the result set into startScn, and fill startRestartScn with the value of START_POINT - 1. Synchronization can then begin from this point.

```yaml
source：
  type: Oracle                                      
  dataSource:
    database: ORCLCDB
    pdbName: ORCLPDB1
    hostname:
      - host: 127.0.0.1                               
        port: 1521  
    linkType: serviceName
    username: system                              
    password: V1SyJt4acNihc54MLSsUEb3J1F2FT41grw==        
  options:
    connectorParameter:
	  startScn: 157983196
	  startRestartScn: 157981183
```

The archived logs corresponding to the specified starting point must not have expired; otherwise, synchronization will fail.



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

Data type compatibility configuration is used only for forward link automatic table creation. For reverse links, if the metadata coverage strategy is set to ignore mode, it may fail due to data type validation.

Configuring data type compatibility may lead to risks of data sync overflow or data insertion failures; please use with caution.

|Data Type Compatibility Configuration |Oracle Data Type |YashanDB Compatible Type |
| -------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------------- |
| options.dataCompatibility. allNumericTypeCompatible=true | FLOAT/BINARY_FLOAT/DOUBLE/BINARY_DOUBLE/NUMBER(P, S)/DEC/NUMERIC/DECIMAL | FLOAT/BINARY_FLOAT/DOUBLE/BINARY_DOUBLE/NUMBER(P, S) |
| options.dataCompatibility. validateStringLength=false    | VARCHAR(size)                                                | VARCHAR(any size)                                    |
| options.dataCompatibility. validateStringLength=false    | NVARCHAR(size)                                               | NVARCHAR(any size)                                   |
| options.dataCompatibility. validateNumberMapBit=true     | NUMBER(P, S)/DEC/NUMERIC/DECIMAL                             | BIT(size)                                            |
| options.dataCompatibility. validateNumberMapBoolean=true | NUMBER(P, S)/DEC/NUMERIC/DECIMAL                             | BOOLEAN                                              |
| options.dataCompatibility. validateCharMapTime=true      | CHAR(size)                                                   | TIME                                                 |
