The DMC configuration files are categorized into the following two types:

- **Server Start Configuration File**

  The path and name are `{DMC installation directory}/conf/yds_config.yml`, used to configure parameters related to the server, such as the server instance name, listening port, etc. This configuration file will be present by default after deploying the DMC server and has specified default values for each parameter. Users can manually modify it to the actual values; if not modified, all default values will be used for service startup.

- **Task Configuration File**

  The path and name are `{DMC installation directory}/conf/task_config.yml`, used to configure parameters related to migration tasks, such as task name, data source connection information, synchronization scope, data type mapping, etc. This configuration file will also exist by default after deploying the DMC server and contains task configuration templates from different source ends to the target end. Users need to manually modify it to the actual values, and incorrect configurations may lead to failure to start tasks, errors during task execution, or incorrect migration results.

## Configuration Items

According to the classification of configuration files, the configuration items are also divided into the following two categories:

- **Server Start Configuration Items**

  Includes necessary parameter configurations for the DMC server to start. If other parameters need to be modified, the service must be restarted for them to take effect; however, some parameters such as log level can be modified and take effect at any time via CLI commands.

- **Task Start Configuration Items**

  Includes necessary parameter configurations for starting migration tasks or resuming from a breakpoint. If parameters need to be modified, the task must be restarted for them to take effect.

### Server Start Configuration Items

Refer to the contents of the `{DMC installation directory}/conf/yds_config.yml` file for the configuration format. The following are descriptions of each configuration item.

#### instanceName

- Description: The instance name of the DMC server
- Value Range: A string of length within 40, consisting of letters, numbers, and underscores.
- Default Value: None; must be entered.

#### port

- Description: The port number that the DMC server listens on by default after startup, primarily for communication with the client.
- Value Range: A number from 1 to 65535.
- Default Value: 8100.

#### logging.level

- Description: Default log level for DMC server running logs.
- Value Range: {info|debug|warn|error|trace}.
- Default Value: info.

#### logging.audit

- Description: Audit log recording level, records change statements executed by the connector based on the selected level.
- Value Range: {all|ddl|off}; due to the Yaml file specification, the string "off" needs to be enclosed in quotes, like 'off'; otherwise, parameter validation will report an error.
- Default Value: off.

#### logging.auditLogAllEffectTime

- Description: Effective time for the audit log all mode, unit: minutes.
- Value Range: A number from 1 to 1440.
- Default Value: 10.

#### jvm

- Description: Adjusting the JVM resources used by the DMC server.
- Value Range: Adjustable JVM parameters see the table below.
- Default Value: None; must be entered.

**JVM Startup Parameters**

JVM (Java Virtual Machine) startup parameters are a set of options used to adjust JVM behavior. These options can be used to adjust heap memory size, garbage collection strategy, JIT compilation optimization, and others. The following lists some parameters that significantly affect DMC; if you want to learn more about usage and tuning techniques, you can view [Java HotSpot VM Options](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html).

|Parameter Filling Method |Parameter Introduction |
| -------------------------------------- | ---------------------------------------- |
| -XX:+HeapDumpOnOutOfMemoryError                 | Outputs a heap memory snapshot when heap memory overflows. |
| -XX:HeapDumpPath=*heapdumpPath*                 | Path for outputting heap memory snapshot when heap memory overflows. |
| -Xms*size* or -XX:InitialHeapSize=*size*       | Initial memory value of the heap area.                |
| -Xmx*size* or -XX:MaxHeapSize=*size*            | Maximum allocatable memory value for the heap area.   |
| -XX:MaxDirectMemorySize=*size*                   | Maximum allocatable memory value for direct memory.    |
| -XX:+UseZGC                                    | Uses the ZGC garbage collector.                         |

#### options.timezone

- Description: Default runtime time zone of the JVM.
- Value Range: Format as full name (e.g. "America/Los_Angeles") or custom ID (e.g. "GMT-8:00"), specific to the description of java.util.TimeZone class.
- Default Value: GMT+8:00.

#### plugins.prometheusExporter.port

- Description: Prometheus monitoring port.
- Value Range: A number from 1 to 65535.
- Default Value: 8101.

#### plugins.prometheusExporter.enable

- Description: Whether to enable Prometheus monitoring service.
- Value Range: {true|false}.
- Default Value: false.

#### plugins.prometheusExporter.addJvmMetrics

- Description: Whether to enable JVM monitoring service; enabling it will cause a decrease in application performance.
- Value Range: {true|false}.
- Default Value: false.

### Task Start Configuration Items

Refer to the contents of the `{DMC installation directory}/conf/task_config.yml` file for the configuration format. The following are descriptions of each configuration item.

#### taskName

- Description: Migration task name.
- Value Range: A string of length within 40 characters, consisting of letters, numbers, underscores, and English.
- Default Value: None; must be entered.

#### source.type

- Description: Source  connector name.
- Value Range: {YashanDB|Oracle|MySQL|YashanDBMySQLMode}.
- Default Value: None; must be entered.

#### source.dataSource.hostname.host

- Description: Source  connector's host name.
- Value Range: Domain name or IP.
- Default Value: None; must be entered.

#### source.dataSource.hostname.port

- Description: Source  connector's listening port number.
- Value Range: Domain name or IP.
- Default Value: None; must be entered.

#### source.dataSource.linkType

- Description: Source  connection method, suitable for connectors with multiple connection methods. For example, Oracle can connect using ServiceName, SID, etc.
- Value Range: For Oracle connectors, it is {ServiceName|SID}; other connectors cannot be configured.
- Default Value: None; determined by the related connector if it is a required input.

#### source.dataSource.username

- Description: Username for connecting to the source .
- Value Range: String.
- Default Value: None; must be entered.

#### source.dataSource.password

- Description: Password for connecting to the source .
- Value Range: Encrypted text generated using the [encryptpwd command](User Interfaces.html#encryptpwd).
- Default Value: None; must be entered.

#### source.dataSource.database

- Description: Database name for the source .
- Value Range: For Oracle connectors based on the respective linkType value; other connectors cannot be configured.
- Default Value: None; determined by the related connector if it is a required input.

#### source.dataSource.properties

- Description: Connection parameters for the source , often used to set connection attributes such as SSL/TLS encrypted communication.
- Value Range: See the corresponding JDBC documentation for each connector.
- Default Value: None; optional input.

#### source.dataSource.properties.internal\_logon

- Description: Specifies the role when logging in as the SYS user for the source . When using the SYS user to connect to the Oracle database, configure this parameter as SYSDBA, or configure the username parameter as SYS AS SYSDBA.
- Value Range: For Oracle connectors, it can be SYSDBA; other connectors cannot be configured.
- Default Value: None; optional input.

#### source.dataSource.pool.maximumPoolSize

- Description: Maximum number of connections in the connection pool for the source .
  - When syncMode=*metadata*, the value should be greater than metadataParallelism.
  - When syncMode=*snapshot*, the value should be greater than MAX(metadataParallelism, snapshotParallelism*2).
  - When syncMode=*increment*, the value should be greater than MAX(metadataParallelism, incrementParallelism).
  - When syncMode=*initial*, the value should be greater than MAX(metadataParallelism, incrementParallelism, snapshotParallelism*2).
  - Otherwise, the task may fail due to insufficient connections.

- Value Range: A number from 10 to 2^31-1.
- Default Value: 260.

####  source.dataSource.pool.minimumIdle

- Description: Minimum idle connections in the connection pool for the source . When task concurrency is high, it is recommended to increase this parameter's value appropriately. For example, if concurrency=128, minimumIdle=128.
- Value Range: A number from 1 to 2^31-1.
- Default Value: 10.

#### source.dataSource.pool.connectionTimeoutMs

- Description: Connection timeout for obtaining connections from the connection pool for the source , unit: milliseconds.
- Value Range: A number greater than 250.
- Default Value: 60000.

#### source.dataSource.pdbName

- Description: When synchronizing data from a PDB database using a CDB database connection, specify the PDB database name.
- Value Range: Existing PDB name in Oracle.
- Default Value: null.

#### source.options.fetchSize

- Description: JDBC batch processing size for full data synchronization of the source; to avoid OOM issues, MySQL database uses streaming processing, making this parameter ineffective.
- Value Range: {-1|A number from 1 to 10240}.
- Default Value: 1024.

#### source.options.metadataParallelism

- Description: Source  metadata synchronization concurrency.
- Value Range: A number from 1 to 128.
- Default Value: 8.

#### source.options.incrementSyncCurrentTimestampDefaultStrategy

- Description: Default strategy for handling the timestamp type current_timestamp during source incremental synchronization.
  - data_first: Default is data priority, ensuring data consistency and final structure consistency.
  - report_error: Does not support the current time function; errors or fault tolerance occur.
  - structure_first: Structure is prioritized, ensuring structure consistency but not data consistency.

- Value Range: {data_first|report_error|structure_first}.
- Default Value: data_first.

#### source.options.ignoreFlashback

- Description: When synchronizing system table data, whether to ignore flashback queries. The default value is false, which means that system table data is obtained through flashback queries for synchronization. Only when it can be ensured that the table structure within the synchronization range will not be modified during the query of system tables, can this value be considered to be set to true (that is, not using flashback queries); otherwise, there may be a risk of inconsistent data synchronization.
- Value Range:{true|false}
- Default Value: false.

<span id="connectorparametersource" name="connectorparametersource" class="yaslink"></span>
#### source.options.connectorParameter

Source connector parameters, see detailed descriptions below:

::: tabs

== YashanDB

**source.options.connectorParameter.ystreamParallel**

- Description: Redo parsing thread concurrency. Increasing the concurrency of parsing threads can enhance performance but will consume more resources; please configure this value reasonably.
- Value Range: A number from 1 to 128.
- Default Value: 4.

**source.options.connectorParameter.txnAgeSpillThreshold**

- Description: Time threshold for LCR overflow typically, unit: seconds. During parsing, if a transaction does not commit for a long time and exceeds this value, all LCRs for that transaction will overflow into the system table for persistence and release memory. The logs corresponding to this LCR no longer need to be parsed again; users can clear the archive log files as needed.
- Value Range: A number from 1 to 100000.
- Default Value: 600.

**source.options.connectorParameter.txnLcrSpillThreshold**

- Description: Memory usage threshold for LCR overflow, unit: bytes. During parsing, if a transaction occupies memory exceeding this value, all its LCRs will overflow into the system table for persistence and release memory. The logs corresponding to this LCR do not need to be parsed again; users can clear the archive log files as needed.
- Value Range: 32K to 1T.
- Default Value: 128M.

**source.options.connectorParameter.checkpointInterval**

- Description: The interval for performing Checkpoint, unit: seconds. Each Checkpoint will persist the latest restart recovery point in the system table. After the client sets the latest applied position, it needs to wait for one Checkpoint interval to be recognized by the database.
- Value Range: A number from 1 to 3600.
- Default Value: 3.

**source.options.connectorParameter.queueSize**

- Description: The size of the asynchronous queue for carrying LCRs. The YStream client enables two asynchronous queues to process data in layers, enhancing throughput.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 128.

**source.options.connectorParameter.clientResponseTimeout**

- Description: The maximum timeout period for the YStream client waiting for a response from the server, unit: seconds.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 60.

**source.options.connectorParameter.pollTimeout**

- Description: The maximum blocking time for the YStream client to retrieve data from the queue, unit: seconds.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 3.

**source.options.connectorParameter.startScn**

- Description:The starting position where the YStream client retrieves data from the queue.
- Value Range:A number from 0 to 2<sup>63</sup>-1.
- Default Value: None; optional input.

**source.options.connectorParameter.startInstanceId**

- Description:Used to specify the instance ID (not recommended for configuration).
- Value Range:A number from 0 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.startGroupOffset**

- Description:Used to specify the group offset (not recommended for configuration)
- Value Range:A number from 0 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.startGroupLsn**

- Description:Used to specify the group LSN (not recommended for configuration)
- Value Range:A number from 0 to 2<sup>31</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.startBatchRowId**

- Description:Used to specify the batch ROWID (not recommended for configuration)
- Value Range:A number from 0 to 2<sup>31</sup>-1.
- Default Value: 0.

== Oracle

**source.options.connectorParameter.transactionSwapPath**

- Description: The path for large transaction data swappable storage.
- Value Range: The file path on the machine where the DMC server is located; please ensure operational privileges for this path.
- Default Value: `{DMC installation directory}/tmp/{DMC instance name}/{migration task name}`.

**source.options.connectorParameter.transactionSwapThreshold**

- Description: When DML exceeds this value within a single transaction, DMC will process the transaction data swappable storage, thus preventing JVM memory overflow risks.
- Value Range: 32000B to 2<sup>64</sup>-1B, supporting B/K/KB/M/MB/G/GB as units, case insensitive.
- Default Value: 32000B.

**source.options.connectorParameter.connectType**

- Description: Specifies which framework Oracle uses to synchronize data.
- Value Range: LOGMINER.
- Default Value: LOGMINER.

**source.options.connectorParameter.interceptionCreateSequence**

- Description: Specifies whether to intercept the creation of sequences.
- Value Range: {true|false}.
- Default Value: false.

**source.options.connectorParameter._filterDdlPattern**

- Description: Hidden parameter; configuration is not recommended. Incremental DDL filtering rules are used to avoid unsupported incremental DDL scenarios. If incremental DDL matches any regular expressions, it will be filtered and warned, and logged; otherwise, it will be synchronized downstream.
- Value Range: A list of regular expressions:
    - ^CREATE\s+TRIGGER.\*
	- ^CREATE\s+PROCEDURE.\*
	- ^CREATE\s+VIEW.\*
- Default Value: None; optional input.

**source.options.connectorParameter.manualDdl.useCurrentMetadata**

- Description: Whether to use current source database metadata to manually skip DDL.
- Value Range: {true|false}.
- Default Value: false.

**source.options.connectorParameter.manualDdl.changeType**

- Description: Type of manual DDL change.
- Value Range: {CREATE_TABLE|ALTER_TABLE|DROP_TABLE|RENAME_TABLE}.
- Default Value: None; optional input.

**source.options.connectorParameter.manualDdl.schema**

- Description: Database name where the manual DDL table is located.
- Value Range: Already existing database name.
- Default Value: None; optional input.

**source.options.connectorParameter.manualDdl.name**

- Description: Name of the manual DDL table.
- Value Range: Already existing table name.
- Default Value: None; optional input.

**source.options.connectorParameter.manualDdl.originSchema**

- Description: The original database name of the manual DDL table when source.options.connectorParameter.manualDdl.changeType is RENAME_TABLE.
- Value Range: Already existing database name.
- Default Value: None; optional input.

**source.options.connectorParameter.manualDdl.originName**

- Description: The original table name when source.options.connectorParameter.manualDdl.changeType is RENAME_TABLE.
- Value Range: Already existing table name.
- Default Value: None; optional input.

**source.options.connectorParameter.startScn**

- Description: The starting position for Oracle Stream capture and synchronization.
- Value Range: 0-2<sup>63</sup>-1 
- Default Value: None; optional input.

**source.options.connectorParameter.startRestartScn**

- Description: The starting position for Oracle Stream to capture incremental data using LogMiner.
- Value Range: 0-2<sup>63</sup>-1 
- Default Value: Same with source.options.connectorParameter.startScn.

**source.options.connectorParameter.lgmConnectionResetLoop**

- Description: Oracle Stream uses Logminer to repeatedly extract incremental data and reset the number of database connections.
- Value Range: 10-100
- Default Value: 50.

**source.options.connectorParameter.failWhenNotRollbackUndoEvent**

- Description: When Oracle Stream parses the rollback event through Logminer but the event cannot be executed, it will trigger an execution failure.
- Value Range: {true|false}
- Default Value: true.

== MySQL

**source.options.connectorParameter.timezone**

- Description: Specifies the time zone used when reading data for the Timestamp type; the effective method is to set the session-level parameter @@session.time_zone.
- Value Range: Only supports GMT-based time zones.
- Default Value: GMT+8:00.

**source.options.connectorParameter.serverId**

- Description: serverId for the MySQL binlog connector; please fill in a value different from other migration tasks to avoid unknown exceptions.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: None; must be entered.

**source.options.connectorParameter.queueSize**

- Description: Size of the asynchronous queue for holding MySQL change events.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 128.

**source.options.connectorParameter.connectTimeout**

- Description: Maximum timeout period in milliseconds that the MySQL Stream client waits for a response from the server.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 3000.

**source.options.connectorParameter.pollTimeout**

- Description: Maximum blocking time that the MySQL Stream client waits to retrieve data from the queue, unit: seconds.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 3.

**source.options.connectorParameter.lobDataSwapPath**

- Description: File path for LOB data swappable storage.
- Value Range: The file path on the machine where the DMC server is located; please ensure operational privileges for this path.
- Default Value: `{DMC installation directory}/tmp/{DMC instance name}/{migration task name}`.

**source.options.connectorParameter.lobDataMaxThresholdValue**

- Description: When the size of LOB data exceeds this value, DMC will process the LOB data for swappable storage to avoid JVM memory overflow risks.
- Value Range: 32000B to 2<sup>32</sup>-1B, supporting B/K/KB/M/MB/G/GB as units, case insensitive.
- Default Value: 32000B.

**source.options.connectorParameter.interceptFloatSync**

- Description: Whether to intercept floating point type synchronization during the incremental synchronization phase.
  - When a float/double type exists as the primary key in the MySQL table, not intercepting might lead to data inconsistency or primary key conflicts. In MySQL tables without primary keys where float/double types exist, it might lead to data inconsistency.

  - When interception is enabled, DMC will log the intercept information into the warning log ({DMC installation directory}/logs/{DMC instance name}/{migration task name}/warn/xxx.log), and the final migration task will fail and throw the corresponding exception information.

  - Users can obtain float/double type tables from the warning log, change them to decimal types, or add tables to the task blacklist and then restart the migration task.

- Value Range: {true|false}.
- Default Value: true.

**source.options.connectorParameter.convertFloatToClientFormat**

- Description: Whether to output floating-point numbers in MySQL Client format (e.g., MySQL JDBC) during the incremental synchronization phase, default is false. When true, floating-point outputs during the incremental phase will be consistent with the values queried by the MySQL client. This ensures that full synchronization data can be successfully updated or deleted during the incremental phase, although it may lead to some loss of precision. When false, floating-point numbers will not be converted during the incremental phase.
- Value Range: {true|false}.
- Default Value: false.

**source.options.connectorParameter.snapshotLobParallelism**

- Description: Full concurrency for LOB tables. The default value is the same as the overall concurrency (i.e., options.snapshotParallelism). Synchronization of LOB data may lead to memory overflow risks; adjust the full concurrency of LOB tables based on available memory.
- Value Range: A number less than or equal to options.snapshotParallelism.
- Default Value: Equal to options.snapshotParallelism.

**source.options.connectorParameter.maxRecentRetriableCount**

- Description: Maximum retry count within one hour for the MySQL Stream Client when encountering IOException. Default value is -1, meaning no limit on retries. The value 0 disables retries, while values from 1 to 2<sup>31</sup>-1 specify the maximum retries within one hour when encountering IOException.
- Value Range: {-1|0|1 to 2<sup>31</sup>-1}.
- Default Value: -1.

**source.options.connectorParameter.readLock.enable**

- Description: Used to set the synchronization mode for the MySQL database: locked synchronization or unlocked synchronization.
  - In locked synchronization mode, at the initialization stage of the task, DMC will pre-apply some connections for locked synchronization. The number of connections requested is related to the source concurrency of metadata sync `source.options.metadataParallelism` and overall concurrency `options.snapshotParallelism`, calculated as: connections requested = `max(source.options.metadataParallelism, options.snapshotParallelism * 2) + 2`.

  - In unlocked synchronization mode, DMC cannot guarantee data consistency, and users must ensure that no business data is written and no DDL changes occur before the DMC task enters the incremental synchronization phase.

  - It is strongly recommended to use locked synchronization mode.

  - In locked synchronization mode, executing DDL operations at the task initialization or metadata synchronization stage may result in metadata validation failures, data synchronization failures, or incremental synchronization failures. Executing DDL operations during the full synchronization phase that includes CREATE TABLE will not affect the task; otherwise, it may lead to synchronization failures.

- Value Range: {true|false}.
- Default Value: true.

**source.options.connectorParameter.readLock.lockTimeout**

- Description: Lock timeout in locked synchronization mode, unit: seconds.
- Value Range: A number from 1 to 180.
- Default Value: 10.

**source.options.connectorParameter.auxInfoUseDatabase**

- Description: Specifies the database for the heartbeat table.
- Value Range: Database names supported by MySQL.
- Default Value: $YDS_AUX_INFO.

**source.options.connectorParameter.binlogFileName**

- Description: Specifies the filename for capturing binary logs.
- Value Range: MySQL binlog filename.
- Default Value: None; optional input.

**source.options.connectorParameter.beginPosition**

- Description: Specifies the starting point for capturing binary logs.
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.binlogPosition**

- Description: Specifies the starting point for capturing a specific DML event (not recommended to configure).
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.logicPosition**

- Description: Logical position of DML and DDL in binary log events (not recommended to configure).
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: 0.

== YashanDBMySQLMode

**source.options.connectorParameter.connectTimeout**

- Description: Maximum timeout period in milliseconds for the YashanDB (MySQL mode) Stream client waiting for a response from the server.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 3000.

**source.options.connectorParameter.pollTimeout**

- Description: Maximum blocking time for the YashanDB (MySQL mode) Stream client to retrieve data from the queue, unit: seconds.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 3.

**source.options.connectorParameter.snapshotLobParallelism**

- Description: Full concurrency for LOB tables. The default value is the same as the overall concurrency (i.e., options.snapshotParallelism). Synchronization of LOB data may lead to memory overflow risks; adjust the full concurrency of LOB tables based on available memory.
- Value Range: A number less than or equal to options.snapshotParallelism.
- Default Value: Equal to options.snapshotParallelism.

**source.options.connectorParameter.startScn**

- Description: The starting position from which the YStream client retrieves data from the queue.
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: None; optional input.

**source.options.connectorParameter.startInstanceId**

- Description: Used to specify instance ID (not recommended to configure).
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.startGroupOffset**

- Description: Specifies the group offset (not recommended to configure).
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.startGroupLsn**

- Description: Used to specify the group LSN (not recommended to configure).
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.startBatchRowId**

- Description: Used to specify batch ROWID (not recommended to configure).
- Value Range: 1 to 2<sup>63</sup>-1.
- Default Value: 0.

**source.options.connectorParameter.ignoreExistYstream**

- Description: Whether to ignore existing YStreams. If true, existing YStreams with the same name will be deleted and recreated; if false, the YStream created by the user will be directly used. This parameter only takes effect when specifying startScn.
- Value Range: true/false.
- Default Value: false.

== RelayLog

**source.options.connectorParameter.bashPath**

- Description: Path where the relay log data file is located.
- Value Range: The file path on the machine where DMC is located; please ensure operational privileges for this path.
- Default Value: $YDS_HOME/relaylog/INSTANCE_NAME/task_name.

**source.options.connectorParameter.deserializeClass**

- Description: Relay log data deserialization class.
- Value Range: com.sics.yds.connector.relaylog.function.increment.event.deserialize.JsonDeserializer.
- Default Value: com.sics.yds.connector.relaylog.function.increment.event.deserialize.JsonDeserializer.

:::

#### sink.type

- Description: Target  connector name.
- Value Range: {YashanDB|Oracle|MySQL|YashanDBMySQLMode}.
- Default Value: None; must be entered..

#### sink.dataSource.hostname.host

- Description: Target  connector's host name.
- Value Range: Domain name or IP.
- Default Value: None; must be entered..

#### sink.dataSource.hostname.port

- Description: Target  connector's listening port number.
- Value Range: Domain name or IP.
- Default Value: None; must be entered..

#### sink.dataSource.linkType

- Description: Target  connection method, suitable for connectors with multiple connection methods. For example, Oracle can connect using ServiceName, SID, etc.
- Value Range: For Oracle connectors, it is {ServiceName|SID}; other connectors cannot be configured.
- Default Value: None; determined by the related connector if it is a required input..

#### sink.dataSource.username

- Description: Target .
- Value Range: String.
- Default Value: None; must be entered..

#### sink.dataSource.password

- Description: Target .
- Value Range: Encrypted text generated using the [encryptpwd command](User Interfaces.html#encryptpwd).
- Default Value: None; must be entered..

#### sink.dataSource.database

- Description: Target .
- Value Range: For Oracle connectors based on the respective linkType value; other connectors cannot be configured.
- Default Value: None; determined by the related connector if it is a required input..

#### sink.dataSource.properties

- Description: Target , often used to set connection attributes such as SSL/TLS encrypted communication.
- Value Range: See the corresponding JDBC documentation for each connector.
- Default Value: None; optional input..

#### sink.dataSource.properties.internal\_logon

- Description: Target . When using the SYS user to connect to the Oracle database, configure this parameter as SYSDBA, or configure the username parameter as SYS AS SYSDBA.
- Value Range: For Oracle connectors, it can be SYSDBA; other connectors cannot be configured.
- Default Value: None; optional input..

#### sink.dataSource.pool.maximumPoolSize

- Description: Target .
  - When syncMode=*metadata*, the value should be greater than metadataParallelism.
  - When syncMode=*snapshot*, the value should be greater than MAX(metadataParallelism, snapshotParallelism*2).
  - When syncMode=*increment*, the value should be greater than MAX(metadataParallelism, incrementParallelism).
  - When syncMode=*initial*, the value should be greater than MAX(metadataParallelism, incrementParallelism, snapshotParallelism*2).
  - Otherwise, the task may fail due to insufficient connections.

- Value Range: A number from 10 to 2^31-1.
- Default Value: 260..

#### sink.dataSource.pool.minimumIdle

- Description: Target . When task concurrency is high, it is recommended to increase this parameter's value appropriately. For example, if concurrency=128, minimumIdle=128.
- Value Range: A number from 1 to 2^31-1.
- Default Value: 10..

#### sink.dataSource.pool.connectionTimeoutMs

- Description: Target , unit: milliseconds.
- Value Range: A number greater than 250.
- Default Value: 60000..

#### sink.options.metadataParallelism

- Description: Target  metadata synchronization concurrency.
- Value Range: A number from 1 to 128.
- Default Value: 8..

#### sink.options.snapshotBatchRows

- Description: Target full data synchronization transaction batch processing size.
- Value Range: A number from 1 to 100000.
- Default Value: 10000.

#### sink.options.incrementParallelism

- Description: Target incremental data synchronization concurrency.
- Value Range: A number from 1 to 128.
- Default Value: 4.

#### sink.options.incrementMaxBatchRows

- Description: Maximum transaction batch processing size for target incremental data synchronization (under data final consistency strategy).
- Value Range: A number from 1 to 100000.
- Default Value: 100.

#### sink.options.incrementCommitMaxTimeDelay

- Description: Maximum transaction batch processing time for target incremental data synchronization (under data final consistency strategy).
- Value Range: A number from 1 to 60.
- Default Value: 1.

#### sink.options.incrementMaxUseCursors

- Description: Maximum size of held cursors for target incremental data synchronization.
- Value Range: A number from 1 to 100000, must be at least sink.options.incrementParallelism + 2.
- Default Value: 200.

#### sink.options.lobSizeThreshold

- Description: Under full data synchronization, incremental data synchronization with data consistency (transaction-consistent) strategy, LOB insertion uses batch submission. This parameter value limits when a single-threaded accumulated LOB size exceeds a certain size to trigger a batch submission, preventing temporary storage overload from some connectors. Note that this value checks each row.
- Value Range: 1K to 32G.
- Default Value: 64M.

#### sink.options.checkDml

- Description: During incremental DML synchronization, whether to check the number of affected rows for synchronization, i.e., check if DML takes effect on the target connector.
- Value Range: {true|false}.
- Default Value: true.

#### sink.options.dmlPrepare

- Description: The target end executor of the incremental synchronization algorithm executes the replay process. BATCH employs DML merging and batch insertion replay techniques, which may encounter situations where the table contains multiple constraints, and concurrent replay mutual exclusivity leads to constraint conflicts.
- Value Range: {BATCH|SINGLE}.
- Default Value: When the value of options.consistencyLevel is "data-consistent", it is set to "BATCH"; otherwise, it is set to "SINGLE".

<span id="connectorparametertarget" name="connectorparametertarget" class="yaslink"></span>

#### sink.options.connectorParameter

Target connector parameters, see detailed descriptions below:

::: tabs

== YashanDBMySQLMode

**sink.options.connectorParameter.auxInfoUseDatabase**

- Description: Specifies the database for storing breakpoint resume auxiliary table.
- Value Range: Database names supported by MySQL.
- Default Value: $YDS_AUX_INFO.

== YashanDB

**sink.options.connectorParameter.checkEmptyString**

- Description: Whether to check empty string synchronization. true means that empty string synchronization is only allowed when the YashanDB parameter EMPTY_STRING_AS_NULL is FALSE; when TRUE, the task will fail during the pre-check stage; false means not to check empty string synchronization, synchronization behavior depends on the YashanDB parameter EMPTY_STRING_AS_NULL. If EMPTY_STRING_AS_NULL is TRUE, YashanDB will treat empty strings as NULL; thus, unexpected exceptions may occur during synchronization, leading to task errors or data inconsistencies, e.g., violating non-null constraints, update/delete statements not matching corresponding columns.
- Value Range: {true|false}.
- Default Value: $true.

== MySQL

**sink.options.connectorParameter.auxInfoUseDatabase**

- Description: Specifies the database storing the breakpoint resume auxiliary table.
- Value Range: Database names supported by MySQL.
- Default Value: $YDS_AUX_INFO.

== RelayLog

**sink.options.connectorParameter.bashPath**

- Description: Path where the relay log data file is located.
- Value Range: The file path on the machine where DMC is located; please ensure operational privileges for this path.
- Default Value: $YDS_HOME/relaylog/INSTANCE_NAME/task_name.

**sink.options.connectorParameter.serializeClass**

- Description: Relay log data serialization class.
- Value Range: com.sics.yds.connector.relaylog.function.increment.event.serialize.JsonSerializer.
- Default Value: com.sics.yds.connector.relaylog.function.increment.event.serialize.JsonSerializer.

**sink.options.repError.dml.type**

- Description: The matching type for DML fault tolerance rules during incremental DML synchronization. Currently, only matching by database error code (errorCode) is supported, and this is currently applicable only for Oracle target endpoints.
- Value Range: errorCode.
- Default Value: None, must be explicitly configured.

**sink.options.repError.dml.content**

- Description:The specific content to match against the corresponding type. Since currently only errorCode is supported for type, this field should be filled with the Oracle database error code to be tolerated (e.g., 00104). Refer to the official documentation for error code descriptions: https://docs.oracle.com/en/error-help/db/ora-index.html.
- Value Range: Valid Oracle error code string.
- Default Value: None, must be explicitly configured.

**sink.options.repError.dml.action**

- Description: The action the system takes when the specified error (type and content) is matched.
- Value Range: ignore (Only ignores the error without any further action), record (Records the information of the failed SQL), retryop (Records the information of the failed SQL and automatically retries the operation).
- Default Value: None, must be explicitly configured.

**sink.options.repError.dml.actionParameter**

- Description: The parameter list corresponding to the action. Currently, this parameter is only required when action is configured as retryop (retry operation). The parameter is an array type, but the retryop action will only use the first element of the array as the maximum retry count.
- Value Range: A numeric array, e.g., [2]. The first element should be a positive integer representing the retry count.
- Default Value: None. Must be configured when action is retryop.

:::

#### options.syncMode

- Description: Synchronization mode of the migration task. Among them:
  - initial: Includes metadata synchronization, full data synchronization, and incremental data synchronization. 
    DMC can seamlessly connect snapshot data and incremental data started under this mode; full data synchronization uses a global read lock (FTWRL) to connect the logs of the incremental synchronization.
  - metadata: Includes only metadata synchronization.
  - snapshot: Includes metadata synchronization and full data synchronization. 
    DMC can carry out full data synchronization of the snapshot data started under this mode; full data synchronization utilizes a global read lock (FTWRL).
  - increment: Includes metadata synchronization and incremental data synchronization. 
    DMC will parse and read incremental data from the current binlog when starting this task (query `show master status`).
	
- Value Range: {initial|metadata|snapshot|increment}.
- Default Value: None; must be entered.

#### options.conflictStrategy

- Description: Metadata synchronization strategy.
  - ignore indicates that when the target side does not have the object to be synchronized, it will be created; if it exists, synchronization for that object will be skipped.
  - override indicates that when the target side does not have the object to be synchronized, it will be created; if it exists, that object will be deleted and then synchronized to the target side.

- Value Range: {ignore|override}.
- Default Value: None; must be entered.

#### options.truncate

- Description: Data synchronization strategy; this parameter only takes effect when `options.conflictStrategy` is ignore.
  - true indicates that if the target table for synchronization has existing data, it will be emptied before data synchronization.
  - false indicates that data will be directly updated into the target table without special handling of existing data.

- Value Range: {true|false}.
- Default Value: true.

#### options.consistencyLevel

- Description: Incremental data synchronization consistency level:
  - Data Final Consistency, ensuring the final consistency of data from the source to the target, using a partition DML merge batch submission technique for more throughput.
  - Transaction Instant Consistency, ensuring the instant consistency of transactions from the source to the target, using grouped concurrent submission techniques for more throughput.
  - Transaction Serial Consistency, ensuring strict serializability of transactions from the source to the target, i.e., maintaining sequential consistency.

- Value Range: {data-consistent|transaction-consistent|transaction-serialize}.
- Default Value: data-consistent.

#### options.snapshotParallelism

- Description: Full data synchronization concurrency.
- Value Range: A number from 1 to 128.
- Default Value: 4.

#### options.oldestActiveTxnTime

- Description: The maximum time difference allowed between the oldest active transaction's system change number and the current system change number when starting the migration task, approximately the time that the oldest active transaction has been present, unit: minutes.
- Value Range: A number from 1 to 1440.
- Default Value: 10.

#### options.ddlFaultTolerance

- Description: Fault tolerance strategy for unsupported incremental DDLs.
  - none indicates no fault tolerance; if unsupported DDL occurs, the migration task reports an error and stops running.
  - ignore_table_all_operations indicates to ignore the DDL along with all subsequent DML and DDL operations on the table involved in the DDL until DROP TABLE is received.

- Value Range: {none|ignore_table_all_operations}.
- Default Value: none.

#### options.syncIgnoreColumn

- Description: Specifies columns to ignore during synchronization.
- Value Range: Column names that exist in the database.
- Default Value: None; optional input.

#### options.snapshotCondition

- Description: Specifies global full-scale filtering conditions.
- Value Range: None.
- Default Value: null.

#### options.upsert.snapshotUseUpsert

- Description: Specifies whether to convert insert to upsert during the full phase.
- Value Range: {true|false}.
- Default Value: false.

#### options.upsert.incrementUseUpsert

- Description: Specify whether to convert insert to upsert during the incremental phase.
  incrInsertUseUpsert: Convert insert to upsert.
  incrUpdateUseUpsert: Convert update to upsert.
  incrInsertUpdateUseUpsert: Convert both insert and update to upsert.
- Value Range: {none|incrInsertUseUpsert|incrUpdateUseUpsert|incrInsertUpdateUseUpsert}
- Default Value: none

#### options.dataCompatibility.decimal

- Description: Compatibility strategy when the precision of the source NUMBER type exceeds the support range of the target NUMBER type.
  - none indicates no compatibility, meaning the migration task will report an error and stop running if it exceeds the support range.
  - overflowMapToVarchar indicates that when exceeding the support range, the source NUMBER type will be converted to target VARCHAR type for synchronization.

- Value Range: {none|overflowMapToVarchar}.
- Default Value: overflowMapToVarchar.

#### options.dataCompatibility.charAndNchar

- Description: Compatibility strategy when the length of source CHAR, NCHAR types exceeds the support range of target CHAR, NCHAR types.
  - none indicates no compatibility; exceeding the support range will cause the migration task to report an error and stop running.
  - overflowMapToVarchar indicates that when exceeding the support range, the source CHAR, NCHAR types will be converted to target VARCHAR type for synchronization.

- Value Range: {none|overflowMapToVarchar}.
- Default Value: overflowMapToVarchar.

#### options.dataCompatibility.validateTimestampPrecision

- Description: Whether to strictly verify the precision when validating TIMESTAMP, DATETIME types in the metadata verification.
  - true indicates to perform strict verification; if verification fails, the task will fail.
  - false indicates no strict verification; in case of precision mismatch, it will only be recorded in the validation report ({DMC installation directory}/logs/{DMC instance name}/{migration task name}{timestamp}/warn/metadata-validate-report-unixts.log) without causing task failure.

- Value Range: {true|false}.
- Default Value: true.

#### options.dataCompatibility.allNumericTypeCompatible

- Description: When validating metadata, whether to consider both source and target sides as numeric types that pass verification.
  - true indicates that data types on both sides are numeric types, which will pass verification without causing task failure.
  - false indicates strict compatibility checks on numeric types.
  - For example, YashanDB -> Oracle synchronization automatically creates tables, where smallint maps to number type; in reverse synchronization from Oracle -> YashanDB without automatic table creation, if the source is number type and the target is smallint type, setting this parameter to false will result in metadata validation failure, while setting it to true will avoid this.
  - Setting it to true may pose data incompatibility risks.

- Value Range: {true|false}.
- Default Value: false.

#### options.dataCompatibility.validateStringLength

- Description: Whether to strictly verify the string length during metadata validation for character types (CHAR/NCHAR/VARCHAR/NVARCHAR).
  - true indicates rigorous verification; if verification fails, the task will fail.
  - false indicates no strict verification; if length mismatch occurs, it will pass verification.
  - Setting to false could lead to char/nchar data inconsistencies during incremental synchronization due to right-padding spaces. It is recommended to set this to false only if the lengths of char/nchar data types match on both the source and target sides.

- Value Range: {true|false}.
- Default Value: true.

#### options.dataCompatibility.validateNumberMapBit

- Description: Whether to pass validation when the source data type is number and the target data type is bit.
  - true indicates it passes validation.
  - false indicates it does not pass validation, causing the task to fail.
  - For example, during automatic table creation in YashanDB -> Oracle synchronization, BIT mapped to number type; in reverse synchronization from Oracle -> YashanDB without automatic table creation, if the source is number type and the target is bit type, setting this parameter to false will cause a metadata validation failure, while setting it to true will avoid this.
  - Setting to true may pose data incompatibility risks.

- Value Range: {true|false}.
- Default Value: false.

#### options.dataCompatibility.validateNumberMapBoolean

- Description: Whether to pass validation when the source data type is number and the target data type is boolean.
  - true indicates it passes validation.
  - false indicates it does not pass validation, causing the task to fail.
  - For example, during automatic table creation in YashanDB -> Oracle synchronization, boolean mapped to number type; in reverse synchronization from Oracle -> YashanDB without automatic table creation, if the source is number type and the target is boolean type, setting this parameter to false will cause a metadata validation failure, while setting it to true will avoid this.
  - Setting to true may pose data incompatibility risks.

- Value Range: {true|false}.
- Default Value: false.

#### options.dataCompatibility.validateCharMapTime

- Description: Whether to pass validation when the source data type is char and the target data type is time.
  - true indicates it passes validation.
  - false indicates it does not pass validation, causing the task to fail.
  - For example, during automatic table creation in YashanDB -> Oracle synchronization, time mapped to char type; in reverse synchronization from Oracle -> YashanDB without automatic table creation, if the source is char type and the target is time type, setting this parameter to false will cause a metadata validation failure, while setting it to true will avoid this.
  - Setting to true may pose data incompatibility risks.

- Value Range: {true|false}.
- Default Value: false.

#### options.dataCompatibility.validateClobMapLong

- Description: Whether to pass validation when the source data type is clob and the target data type is long.
  - true indicates it passes validation.
  - false indicates it does not pass validation, causing the task to fail.
  - For example, during automatic table creation in YashanDB -> Oracle synchronization, long mapped to clob type; in reverse synchronization from Oracle -> YashanDB without automatic table creation, if the source is clob type and the target is long type, setting this parameter to false will cause a metadata validation failure, while setting it to true will avoid this.
  - Setting to true may pose data incompatibility risks.

- Value Range: {true|false}.
- Default Value: false.

#### options.dataCompatibility.validateBlobMapLongRaw

- Description: Whether to pass validation when the source data type is blob and the target data type is long raw.
  - true indicates it passes validation.
  - false indicates it does not pass validation, causing the task to fail.
  - For example, during automatic table creation in YashanDB -> Oracle synchronization, long raw mapped to blob type; in reverse synchronization from Oracle -> YashanDB without automatic table creation, if the source is blob type and the target is long raw type, setting this parameter to false will cause a metadata validation failure, while setting it to true will avoid this.
  - Setting to true may pose data incompatibility risks.
- Value Range: {true|false}.
- Default Value: false.

#### options.dataCompatibility.validateIntMapBit

- Description: Whether to pass validation when the source data type is int and the target data type is bit.
  - true indicates it passes validation.
  - false indicates it does not pass validation, causing the task to fail.
  - For example, in a YashanDB -> MySQL synchronization link with no automatic table creation, if the source column is of INT type and the target column is of BIT type, setting this parameter to false will cause a metadata validation failure, while setting it to true can avoid this issue.
  - Setting to true may pose data incompatibility risks.
- Value Range: {true|false}.
- Default Value: false.

#### options.flashback.enabled

- Description: Whether to enable the recording flashback point functionality. When enabled, you can use the `show flashback` command on the client to query recorded flashback points.
- Value Range: {true|false}.
- Default Value: true.

#### options.flashback.samplingInterval

- Description: Sampling frequency when enabling the recording flashback point functionality.
- Value Range: 1 minute to 356 days.
- Default Value: 24 days.

#### options.snapshotDmlFaultTolerance.enabled

- Description: Whether to enable fault tolerance. This configuration only takes effect when YashanDB is the target and only in snapshot mode. When fault tolerance is enabled, the synchronization and verification of table metadata will not affect other tables during the metadata synchronization phase; during the full synchronization stage, errors in full synchronization of tables will not affect the normal synchronization of other tables (table-level fault tolerance).
- Value Range: {true|false}.
- Default Value: false.

#### options.snapshotDmlFaultTolerance.needFaultTolerant

- Description: Whether to enable table content fault tolerance, which only takes effect when options.snapshotDmlFaultTolerance.enabled is true. When enabled, if the error row count during full data synchronization in the table reaches the fault tolerance threshold (options.snapshotDmlFaultTolerance.errorLimit), the data synchronization task for this table will stop running but will not affect the full data synchronization of other tables.
- Value Range: {true|false}.
- Default Value: false.

#### options.snapshotDmlFaultTolerance.errorLimit

- Description: The fault tolerance threshold for the number of error rows during full data synchronization in the table. This parameter takes effect only when options.snapshotDmlFaultTolerance.enabled is true and options.snapshotDmlFaultTolerance.needFaultTolerant is true. If this parameter value is less than 0, there is no limit on the number of fault-tolerant rows; if it is equal to 0, the table will immediately stop data synchronization upon encountering an error; if it is greater than 0, the table will stop data synchronization only if the number of error rows exceeds this value.
- Value Range: -2147483648 to 2147483647.
- Default Value: 200.

<span id="dataTypeCustomMapping" name="dataTypeCustomMapping" class="yaslink"></span>
#### options.dataTypeCustomMapping

Custom mapping from tool types to target database types for data types, which is used to define mappings for table creation during the metadata stage; creating tables during the incremental DDL phase; adding column types and modifying column types.

**Example**

```yaml
options: # task options
  dataTypeCustomMapping:
    VARCHAR: '"VARCHAR2(" + 4*n + ")"'
```

You can refer to the table below for configuring [JEXL expressions](Other Introductions.html#jexl) for custom data type mappings:

|Tool Type |Configurable Parameters |Parameter Type |Parameter Explanation |JEXL Expression Example |
| ---------------------------------- | ---------- | ------------ | ---------------------------------------- | ---------------------------------------- |
| BIT                            | n                       | Integer        | Bit length                                  | \`BIT(${n})\`                               |
| INT8                               |            |              |                                          | \`TINYINT\`                              |
| INT8_UNSIGNED                     |            |              |                                          | \`TINYINT UNSIGNED\`                     |
| INT16                              |            |              |                                          | \`SMALLINT\`                             |
| INT16_UNSIGNED                    |            |              |                                          | \`SMALLINT UNSIGNED\`                    |
| INT24                              |            |              |                                          | \`MEDIUMINT\`                            |
| INT24_UNSIGNED                    |            |              |                                          | \`IMEDIUMINT UNSIGNED\`                  |
| INT32                              |            |              |                                          | \`INT\`                                  |
| INT32_UNSIGNED                    |            |              |                                          | \`INT UNSIGNED\`                         |
| INT64                              |            |              |                                          | \`BIGINT\`                               |
| INT64_UNSIGNED                    |            |              |                                          | \`BIGINT\`                               |
| FLOAT                          | m,d                     | Integer, Integer | Precision, Scale                            | \`FLOAT(${m}, ${d})\`                       |
| MYSQL_FLOAT                    | m,d                     | Integer, Integer | Precision, Scale                           | \`FLOAT(${m}, ${d})\`                       |
| MYSQL_FLOAT_UNSIGNED           | m,d                     | Integer, Integer | Precision, Scale                           | \`FLOAT(${m}, ${d}) UNSIGNED\`              |
| ORACLE_FLOAT                   | p                       | Integer        | Precision                                   | \`FLOAT(${p})\`                             |
| DOUBLE                         | m,d                     | Integer, Integer | Precision, Scale                            | \`DOUBLE(${m}, ${d})\`                      |
| MYSQL_DOUBLE                   | m,d                     | Integer, Integer | Precision, Scale                           | \`DOUBLE(${m}, ${d})\`                       |
| MYSQL_DOUBLE_UNSIGNED          | m,d                     | Integer, Integer | Precision, Scale                           | \`DOUBLE(${m}, ${d}) UNSIGNED\`              |
| NUMBER                         | p,s                     | Integer, Integer | Precision, Scale                            | \`NUMBER(${p}, ${s})\`                       |
| NUMBER_UNSIGNED                 | p,s                     | Integer, Integer | Precision, Scale                            | \`NUMBER(${p}, ${s}) UNSIGNED\`              |
| CHAR                           | n,c                     | Integer, String | Length, Character type (value: {byte|char}) | \`CHAR(${n} ${c})\`                          |
| VARCHAR                        | n,c                     | Integer, String | Length, Character type                      | \`VARCHAR(${n} ${c})\`                       |
| NCHAR                          | n                       | Integer        | Length                                      | \`NCHAR(${n})\`                              |
| NVARCHAR                       | n                       | Integer        | Length                                      | \`NVARCHAR(${n})\`                           |
| RAW                            | n                       | Integer        | Length                                      | \`RAW(${n})\`                                |
| LONG_RAW                          |            |              |                                          | \`LONG RAW\`                             |
| BOOLEAN                            |            |              |                                          | \`BOOLEAN\`                              |
| DATE                               |            |              |                                          | \`DATE\`                                 |
| MYSQL_DATE                        |            |              |                                          | \`DATE\`                                 |
| TIME                               |            |              |                                          | \`TIME\`                                 |
| MYSQL_TIME                    | p                       | Integer        | Precision                                   | \`TIME(${p})\`                               |
| TIMESTAMP                      | p                       | Integer        | Precision                                   | \`TIMESTAMP(${p})\`                          |
| TIMESTAMP_WITH_TIME_ZONE      | p                       | Integer        | Precision                                   | \`TIMESTAMP(${p}) WITH TIME ZONE\`          |
| TIMESTAMP_WITH_LOCAL_TIME_ZONE | p                       | Integer        | Precision                                   | \`TIMESTAMP(${p}) WITH LOCAL TIME ZONE\`    |
| DATETIME                       | p                       | Integer        | Precision                                   | \`DATETIME(${p})\`                           |
| LONG                               |            |              |                                          | \`LONG\`                                 |
| CLOB                               |            |              |                                          | \`CLOB\`                                 |
| BLOB                               |            |              |                                          | \`BLOB\`                                 |
| NCLOB                              |            |              |                                          | \`NCLOB\`                                |
| INTERVAL_YEAR_TO_MONTH        | y                       | Integer        | Year precision                              | \`INTERVAL YEAR(${y}) TO MONTH\`            |
| INTERVAL_DAY_TO_SECOND        | d,f                     | Integer, Integer | Day precision, Second precision             | \`INTERVAL DAY(${d}) TO SECOND(${f})\`      |
| YEAR                               |            |              |                                          | \`YEAR\`                                 |
| BINARY                         | n                       | Integer        | Length                                      | \`BINARY(${n})\`                             |
| VARBINARY                      | n                       | Integer        | Length                                      | \`VARBINARY(${n})\`                          |
| TINYBLOB                           |            |              |                                          | \`TINYBLOB\`                             |
| MYSQL_BLOB                        |            |              |                                          | \`BLOB\`                                 |
| MEDIUMBLOB                         |            |              |                                          | \`MEDIUMBLOB\`                           |
| LONGBLOB                           |            |              |                                          | \`LONGBLOB\`                             |
| TINYTEXT                           |            |              |                                          | \`TINYTEXT\`                             |
| TEXT                               |            |              |                                          | \`TEXT\`                                 |
| MEDIUMTEXT                         |            |              |                                          | \`MEDIUMTEXT\`                           |
| LONGTEXT                           |            |              |                                          | \`LONGTEXT\`                             |
| ENUM                               |            |              |                                          | \`ENUM\`                                 |
| SET                                |            |              |                                          | \`SET\`                                  |

> **Note**:
>
> 1. It is recommended to handle empty parameter values to prevent potential null pointer issues.
> 2. The custom data type mapping must comply with the current validation strategy, otherwise it will lead to task validation failure.
> 3. Regarding FLOAT and DOUBLE tool types:
>    1. FLOAT and DOUBLE represent types that fully comply with the IEEE754 standard, such as FLOAT and DOUBLE in YashanDB, and BINARY_FLOAT and BINARY_DOUBLE types in Oracle.
>    2. MYSQL_FLOAT and MYSQL_DOUBLE refer specifically to MySQL's FLOAT and DOUBLE types.
>    3. ORACLE_FLOAT primarily refers to the FLOAT type in Oracle, note that it is actually a fixed-point number and a subtype of NUMBER.

#### options.sequenceSync

- Description: Whether to synchronize sequences.
  - When true, synchronize all sequences.
  - When false, do not synchronize any sequences, and if default values in the table structure contain sequences, delete those default values.

- Value Range: {true|false}.
- Default Value: false.

#### options.conflictStrategySequence

- Description: Strategy for synchronizing sequences.
  - When override, it means if a sequence with the same name exists on the target side, it will be deleted and recreated.
  - When ignore, it means if a sequence with the same name exists on the target side, no action will be taken.

- Value Range: {ignore|override}.
- Default Value: options.conflictStrategy's value.

#### options.heartbeatEnabled

- Description: Whether to enable the heartbeat mechanism. When there is no business on the source side, the source will regularly insert into the heartbeat table, which can refresh and observe data and keep track of metrics.
- Value Range: {true|false}.
- Default Value: false.

#### options.heartbeatInterval

- Description: Frequency interval for inserting into the source heartbeat table, in seconds. This frequency interval depends on the maximum blocking interval for obtaining data from the incremental queue. Please ensure this value exceeds `source.options.connectorParameter.pollTimeout`.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 3.

#### options.maxHeartbeatRetry

- Description: Maximum retry times for failures in inserting the source heartbeat table. If the retry count exceeds this limit, it stops trying and stops task execution. This configuration can be used to detect the connection viability of the source database. If the source database is disconnected, surpassing the retry threshold will halt the current synchronization task.
- Value Range: A number from 1 to 2<sup>31</sup>-1.
- Default Value: 10.

#### options.partitionTableInterceptionEnabled

- Description: Whether to intercept partition table synchronization, default is true, meaning it will intercept; otherwise, it will not intercept.
- Value Range: {true|false}.
- Default Value: true.

#### options.\_caseSensitive.\_object

- Description: Hidden parameter; configuration is not recommended. Configure lower to synchronize all schema, table, and sequence names from the source to lowercase. Configure upper to synchronize all to uppercase. Configure default to synchronize all original values from the source.
- Value Range: lower, upper, default.
- Default Value: lower.

#### options.\_caseSensitive.\_column

- Description: Hidden parameter; configuration is not recommended. Configure lower to synchronize all column names from the source to lowercase. Configure upper to synchronize all to uppercase. Configure default to synchronize all original values from the source.
- Value Range: lower, upper, default.
- Default Value: upper.

#### options.checkpointInterval

- Description: The time interval for triggering checkpoints during incremental synchronization.
- Value Range:A number from 1 to 2<sup>31</sup>-1.
- Default Value: 10000.

#### options.snapshotNotSplitTable

- Description: Whether the full synchronization phase synchronizes according to the original size of the data table. The default value is false, which means the target table will be split and synchronized. If set to true, it means no splitting, and synchronization will be performed directly according to the original size of the target table.
- Value Range:{true|false}
- Default Value: false.

<span id="syncRangeParameter" name="syncRangeParameter" class="yaslink"></span>

#### syncRange.schemas.schema

- Description: Name of the schema to be synchronized. During the incremental phase, all tables under this schema (including new ones) will be monitored.
- Value Range: Schema names existing in the source database; multiple can be specified.
- Default Value: None; must be entered.

**Example**

```yaml
syncRange:
  schemas:
    - schema: yds
    - schema: YDS
```

#### syncRange.schemas.mapName

- Description: Name of the mapped schema after mapping, all tables under this schema will be mapped to another schema name.
- Value Range: Schema names existing in the target database.
- Default Value: If not configured, it defaults to the same name as the source schema.

**Example**

```yaml
syncRange:
  schemas:
    - schema: yds
      mapName: t_yds
    - schema: YDS
```

#### syncRange.schemas.tables.table

- Description: Name of the table to be synchronized.
- Value Range: Table names existing in the source database; multiple can be specified.
- Default Value: Not configuring represents synchronizing all tables under the schema.

**Example**

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

#### syncRange.schemas.tables.mapSchema

- Description: Name of the mapped schema for the synchronized table after mapping, indicating mapping to another schema name.
- Value Range: Schema names existing in the target database.
- Default Value: If not configured, it defaults to take the corresponding mapped name of its schema.

**Example**

```yaml
# Source table yds.table is mapped to target table yds1.t_table, source table yds.table1 is mapped to target table t_yds.t_table1.
syncRange:
  schemas:
    - schema: yds
      mapName: t_yds
      tables: 
        - table: table
          mapSchema: yds1
          mapName: t_table
        - table: table1
          mapName: t_table1
```

#### syncRange.schemas.tables.syncIgnoreColumn

- Description: Specifies a column to ignore in the table, which will not be synchronized.
- Value Range: Column names existing in the table.
- Default Value: None; optional input.

**Example**

```yaml
syncRange:
  schemas:
    - schema: TEST_SCHEMA
      tables:
        - table: TEST
          syncIgnoreColumn:
            - ID
            - INSTANCE_ID
```

#### syncRange.schemas.tables.mapName

- Description: Name of the mapped table after synchronization, indicating mapping to another table name. If not configured, it implies the source and target tables have the same name.
- Value Range: A string that meets the naming specification for target database tables.
- Default Value: null.

#### syncRange.tablesBlackList

- Description: Configures a blacklist where the tables in the blacklist will be ignored by the migration task. If not configured, it means not to ignore any tables.
- Value Range: '"schema"."table"', schema and table are enclosed in double quotes, and the entire expression is wrapped in single quotes.
- Default Value: null.

**Example**

```yaml
syncRange:
  tablesBlackList:
    - '"yds"."ignore_table"'
    - '"YDS"."IGNORE_TABLE"'
```

<span id="sequenceSyncRangeParameter" name="sequenceSyncRangeParameter" class="yaslink"></span>
#### sequenceSyncRange.schemas.schema

- Description: Synchronizes all sequences under specified schemas and monitors all sequences under this schema (including new ones) during incremental phases. If not configured, all sequences in all schemas will be synchronized.
- Value Range: Existing schema names in the source database; multiple can be specified.
- Default Value: null.

#### sequenceSyncRange.schemas.mapName

- Description: Maps all sequences in specified schemas to another schema during synchronization; if not configured, the source schema name will be used.
- Value Range: Existing schema names in the target database.
- Default Value: null.

**Example**

```yaml
sequenceSyncRange:
  schemas:
    # Specify the schema for migration; if the target end is empty, use the source end schema name.
    - schema: yds
    # Specify the schema for migration and specify the target schema.
    - schema: yds1
      mapName: yds02
```

#### sequenceSyncRange.schemas.sequences.sequence

- Description: Synchronizes specified sequences, if not configured, all sequences under the pattern will be synchronized.
- Value Range: Existing sequence names in the source database; multiple can be specified.
- Default Value: null.

#### sequenceSyncRange.schemas.sequences.mapSchema

- Description: Maps the schema name of a specified sequence to another schema name, if not configured, original mapping will be used.
- Value Range: Existing schema names in the target database.
- Default Value: null.

#### sequenceSyncRange.schemas.sequences.mapName

- Description: Maps the name of the specified sequence to another name; if not configured, the source sequence name will be kept.
- Value Range: A string that meets the naming specification for target database sequences.
- Default Value: null.

**Example**

```yaml
sequenceSyncRange:
  schemas:
    - schema: yds
      # Specify sequences for migration; if empty denotes the entire schema is to be synchronized.
      sequences:
        - sequence: seq
          # Specify mapped schema for this sequence; if empty uses mapping as per schema field.
          mapSchema: T_YDS1
          # Specify target sequence name; if empty, uses original sequence name.
          mapName: T_SEQ
```

#### sequenceSyncRange.sequencesBlackList.schemas

- Description: Configures a blacklist of schema names, where all sequences under the specified schema will be ignored by the migration task; if not configured, it means not to ignore any schemas.
- Value Range: '"schema"', where schema is enclosed in double quotes, and the entire expression is wrapped in single quotes.
- Default Value: null.

**Example**

```yaml
sequenceSyncRange:
  sequencesBlackList:
    schemas:
      - '"YDS3"'
      - '"YDS4"'
```

#### sequenceSyncRange.sequencesBlackList.sequences

- Description: Configures a blacklist for sequences, where all sequences will be ignored by the migration task; if not configured, it indicates not to ignore any sequences.
- Value Range: '"schema"."sequence"', where schema and sequence are enclosed in double quotes, and the entire expression is wrapped in single quotes.
- Default Value: null.

**Example**

```yaml
sequenceSyncRange:
  sequencesBlackList:
    sequences:
      - '"YDS1"."L_SEQ0"'
      - '"YDS1"."L_SEQ0"'
```

#### transform.dmlFilter.insert

- Description: Support filtering of specified types of DML.
- Value Range: true|false
- Default Value: false

#### transform.dmlFilter.update

- Description: Support filtering of specified types of DML.
- Value Range: true|false
- Default Value: false

#### transform.dmlFilter.delete

- Description: Support filtering of specified types of DML.
- Value Range: true|false
- Default Value: false

#### transform.tableFilter

- Description: Table-level filter rule configuration, supporting schema, name, dmlFilter, and filterRules.
- Default Value: null

#### transform.tableFilter.schema

- Description: The schema name to which the filter rule belongs.
- Value Range: String
- Default Value: None; must be entered when `transform.tableFilter` is not null.

#### transform.tableFilter.name

- Description: List of table names to which the filter rule belongs.
- Value Range: List of strings
- Default Value: None; must be entered when `transform.tableFilter` is not null.

#### transform.tableFilter.dmlFilter

- Description: Table-level DML filter rules, configuration items are the same as global `transform.dmlFilter`. Global `transform.dmlFilter` has higher priority than table-level `transform.tableFilter.dmlFilter`.
- Default Value: null

#### transform.tableFilter.filterRules

- Description: Details of table-level filter rules. When this item is null, it is equivalent to full filtering.
- Default Value: null

#### transform.tableFilter.filterRules.commitTimestamp

- Description: Rule configuration for filtering based on commit timestamp. Generates a local time based on the data commit timestamp, determines whether the local time is within the begin and end given by the user, and if so, filters out the data.
- Default Value: null

#### transform.tableFilter.filterRules.commitTimestamp.begin

- Description: Start time of the filtering period.
- Value Range: Time string, supports wildcard `*`. Format is `YYYY-MM-DD HH:mm:ss`.
  - Year: Integer or `*`.
  - Month: 1-12 or `*`.
  - Day: 1-28/29/30/31 or `*`. Validates leap years (e.g., Feb 29).
  - Hour: 0-23 or `*`.
  - Minute: 0-59 or `*`.
  - Second: 0-59 or `*`.
  - Wildcard constraint: Both begin and end must be wildcards at the corresponding position.
- Default Value: None

#### transform.tableFilter.filterRules.commitTimestamp.end

- Description: End time of the filtering period.
- Value Range: Time string, supports wildcard `*`. Format is `YYYY-MM-DD HH:mm:ss`.
  - Supports special time points:
    - `24:00:00`: Must be wildcard day, zero minute and second.
    - `*:60:00`: Must be wildcard hour, zero second.
    - `*:*:60`: Must be wildcard minute.
  - If it is a wildcard, it is assigned the corresponding value of local time. If end is a boundary value, end overflows upward by one unit.
- Default Value: None

**Example**

```yaml
transform:
#    dmlFilter:                                         # dml filter rules
#      insert: false
#      update: false
#      delete: false
  tableFilter:
    - schema: YDS
      name:
        - YDS_USER
      dmlFilter:
        delete: true
    - schema: YDS1
      name:
        - YDS_USER
      dmlFilter:
        insert: true
        update: true
        delete: false
      filterRules:
        # Strongly related to the local time zone, required to be consistent with the db time zone configuration
          - begin: '*-*-* 23:00:00'
            end: '*-*-* 24:00:00'
          - begin: '*-*-* 00:00:00'
            end: '*-*-* 03:00:00'
          - begin: '*-*-* *:55:00'
            end: '*-*-* *:60:00'
```

