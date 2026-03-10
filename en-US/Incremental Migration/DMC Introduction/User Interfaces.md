DMC provides both CLI and API interfaces for users to initiate various task commands to the server from the DMC client or other application tools.

## DMC CLI

The CLI interface is called by the DMC client and supports both interactive and non-interactive usage modes.

> **Note**:
>
> Both modes provide an optional -p parameter to specify the server port to be accessed. If the specified port does not exist, it does not affect the client's execution of commands, but an error message will be thrown when a server response is needed.

### Interactive Mode

The interactive mode supports executing all DMC commands and allows triggering command hints by double-tapping the Tab key and command completion by tapping the Tab key once. The command format to start the interactive mode is as follows:

::: tabs

== Linux

```shell
$ cd {DMC installation directory}
$ ./bin/start_client.sh [-p <bind_port>]
```

== Windows

```shell
{DMC installation directory}\bin> start_client.bat [-p <bind_port>]
```

:::

Parameter Description:

- -p: Specifies the port that the DMC client uses to access the server. This parameter is optional, and if omitted, it defaults to the port of the most recent access by this client; if there was no recent access, it defaults to 8100.

### Non-Interactive Mode

The non-interactive mode supports executing a subset of DMC commands. The command format to execute in non-interactive mode is as follows:

::: tabs

== Linux

```shell
$ cd {DMC installation directory}
$ ./bin/start_client.sh -c "<cmd1; cmd2; ...>" [-p <bind_port>] [-f]
```

== Windows

```shell
{DMC installation directory}\bin> start_client.bat -c "<cmd1; cmd2; ...>" [-p <bind_port>] [-f]
```

:::

Parameter Description:

- -c: Specifies one or more commands to execute. If the parameter includes the quit command, any commands defined afterward will not be executed.
- -p: Specifies the port that the DMC client uses to access the server. This parameter is optional, and if omitted, it defaults to the port of the most recent access by this client; if there was no recent access, it defaults to 8100.
- -f: If the command parameters contain commands that cannot be executed in non-interactive mode, this option can be used to force execution. However, specifying this option for strictly interactive commands like encryptpwd will cause exceptions.

### CLI Commands

<span id="encryptpwd" name="encryptpwd" class="yaslink"></span>

#### encryptpwd

The encryptpwd command is used to generate encrypted text from a string of characters prompted for input. For certain password configuration items in the DMC configuration file, it is necessary to use this command to generate encrypted text and then copy the text to the configuration file.

**Example**

```shell
YDS@8100> encryptpwd
Enter your password: ******
Your encrypted password:
I8GfD/vcFeFbzGpdbBp79A==
```

#### help [command]

The help command provides usage instructions for all CLI commands.

Parameter Description:

- command: Displays usage instructions for a specified CLI command. If this parameter is not provided, it shows all commands supported by the current version.

#### history

The history command is used to display all CLI commands input in history.

#### ping

The ping command is used to check whether the DMC client can connect to the server.

#### quit

The quit command is used to exit the DMC client. Typing `CTRL+D` has the same effect as this command.

#### remove [--config-path \<task\_config>]

The remove command is used to issue a task removal instruction to the DMC server.

Tasks can only be deleted when they are terminated or failed on the current server.

Removing a task effectively cleans up certain information stored in the source and target connectors. The removed task will not be able to resume, and as long as there are configured source or target endpoints, the execution of the command will have the same effect on any server, so please use this command with caution.

Parameter Description:

- --config-path: Specifies the configuration file path used to start the task. If this parameter is omitted, the configuration path from the most recent execution of the run/resume/remove command will be used; if there was no recent execution, it will take the configuration file under the DMC installation directory where the current client is located.

#### resume [--config-path \<task\_config>] [--ignore-first-ddl] [--manual-ddl]

The resume command is used to issue a breakpoint resume instruction to the DMC server.

Tasks that failed due to unexpected situations like breakpoints or disconnections during incremental data synchronization, or tasks that have been terminated but not deleted, can be resumed using the resume command.

Parameter Description:

- --config-path: Specifies the configuration file path used to start the task. If this parameter is omitted, the configuration path from the most recent execution of the run/resume/remove command will be used; if there was no recent execution, it will take the configuration file under the DMC installation directory where the current client is located.
- --ignore-first-ddl: Specifying this parameter indicates that after resuming the task, the first received DDL will be ignored. This parameter can avoid situations where incremental DDL execution and checkpoint cannot ensure exactly once (e.g., if a CREATE TABLE statement is executed in the source while restoring, the object already exists).
- --manual-ddl: Used in manual DDL scenarios by the user.

#### run [--config-path \<task\_config>]

The run command is used to issue a task startup instruction to the DMC server.

Parameter Description:

- --config-path: Specifies the configuration file path used to start the task. If this parameter is omitted, the configuration path from the most recent execution of the run/resume/remove command will be used; if there was no recent execution, it will take the configuration file under the DMC installation directory where the current client is located.

If the configured options.conflictStrategy parameter is set to override or options.truncate parameter is true, executing the run command will require a second confirmation to avoid overwriting existing data.

Once the DMC server receives the instruction, it will conduct a pre-check before starting the task. The pre-check includes the following:

1. Checking the connectivity between the source and target endpoints.
2. Checking the existence of the schema and table specified in the configuration file.
3. Checking the environment and components required for incremental synchronization.

Tasks that fail the pre-check will report an error during startup.

####  set parameter \<key> \<value>

The set parameter command is used to set various parameter values for DMC components.

Parameter Description:

- key: Specifies the parameter name.
- value: Specifies the parameter value.

The available parameters and their value ranges are as follows:

|Key |Value |Description |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| auditLogLevel     | all/ddl/off [--effect-time \<audit log all effect time(min)>] | Sets the audit log level. --effect-time sets the effective time of the audit log all mode, in minutes, defaulting to the value in the configuration file. |
| bindPort          | 1~65535                                                      | Sets the port number where the DMC service is located.       |
| executeRunTimeout | 1~300                                                        | Sets the timeout for tasks submitted by the run command.      |
| logLevel          | trace/debug/info/warn/error                                  | Sets the log level for task logs.                             |

#### show faultTolerance

The show faultTolerance command is used to display tables that are currently tolerant of incremental DDL along with relevant information.

#### show flashback [--detail]

The show flashback command is used to display the flashback point values recorded by the current task.

For databases like YashanDB and Oracle, DMC provides flashback point recording functionality (this functionality needs to set the parameter options.flashback.enabled = true), allowing users to query the most recently recorded flashback point location.

Parameter Description:

- --detail: When content is excessive, only part is displayed by default. Specifying this parameter shows the full content. The structure is as follows:

|Field |Description |
| :---------------: | :----------------------------------------------------------: |
| sourceType          | Type of the source connector.                                |
| sinkType            | Type of the target connector.                                |
| sourceFlashback     | The most recently recorded flashback point on the source end.|
| sinkFlashback       | The most recently recorded flashback point corresponding to the source at the target end. |
| startSamplingTime   | The sampling time when the flashback point recording started.|
| endSamplingTime     | The sampling time when the flashback point recording ended (the flashback point recording will strictly wait for a complete transaction synchronization to finish). |
| version             | Internal field that records the flashback point version number. |

<span id="showmetrics" name="showmetrics" class="yaslink"></span>

#### show metrics [\<stage>] [--detail]

The show metrics command is used to display various metric data of the current task.

Parameter Description:

- stage: Specifies a stage to display metric information. The value can be metadata (metadata synchronization stage), increment (incremental data synchronization stage), or snapshot (full data synchronization stage). If this parameter is not specified, it defaults to displaying metrics for the current stage of the task.
- --detail: When content is excessive, only part is displayed by default. Specifying this parameter shows the full content.

**Metadata Synchronization Metrics**

Metadata synchronization metrics are mainly used to monitor the state and progress of the task in the metadata stage. The metric content is as follows:

|Field |Description |
| ----------------------------- | ------------------------------------------------------------ |
| SourceType                 | Type of the source connector.                                |
| SourceVersion              | Version of the source connector.                             |
| SinkType                   | Type of the target connector.                                |
| SinkVersion                | Version of the target connector.                             |
| StartTime                  | Start time of the metadata synchronization.                 |
| EndTime                    | End time of the metadata synchronization.                   |
| TotalQueryTables           | Total number of tables queried.                              |
| TotalMigratedTables        | Total number of tables synchronized.                         |
| TotalValidateTables        | Total number of tables validated.                            |
| QueryTablesThroughput(/s)  | Table query speed.                                          |
| MigratedTablesThroughput(/s)| Table synchronization speed.                               |
| ValidateTablesThroughput(/s)| Table validation speed.                                    |
| tableMetadataToleranceMetrics | Tables that are tolerant during the metadata synchronization phase. Each field is defined as follows: <br>1. sourceTable indicates the source table that is tolerant. <br>2. sinkTable indicates the corresponding target table that is tolerant. <br>3. toleranceMsg indicates information about tolerance. <br>4. metadataStage indicates which phase of metadata it is in, divided into metadata synchronization phase and metadata validation phase. |

**Full Data Synchronization Metrics**

Full data synchronization metrics are used to monitor the state and progress of the task in the full data phase. The metric content is as follows:

|Field |Description                                               |
| ----------------- |------------------------------------------------------------------|
| SourceType           | Type of the source connector.                                    |
| SourceVersion        | Version of the source connector.                                 |
| SinkType             | Type of the target connector.                                    |
| SinkVersion          | Version of the target connector.                                 |
| StartTime            | Start time of data synchronization.                              |
| EndTime              | End time of data synchronization.                                |
| MigratingTableList   | Tables currently being synchronized.                             |
| MigratedTables       | Total number of tables that have been synchronized successfully. |
| MigratedTableList       | Total tables that have been synchronized successfully.           |
| WaitMigrateTableList       | Unsynchronized table.                                            |
| FailMigrateTable       | Tables that failed to synchronize                                            |
| TotalTables          | Total number of tables to be synchronized.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| MigratedByteSize     | Amount of data that has been synchronized.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| MigratedRows         | Total number of data rows that have been synchronized.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ThroughputAvg           | Average synchronization speed (bytes, unit adaptive).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| RPSAvg                  | Average synchronization speed (rows).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Throughput      | Data synchronization speed in the past 10 seconds (byte, unit adaptive).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| RPS                  | Synchronization speed in the past 10 seconds (rows).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| SingleTableMigrateMetrics | Migration metrics for tables in the full synchronization phase. Each field is defined as follows: <br>1. objName indicates the name of the target table. <br>2. migratedBytes denotes the total migrated data size, in B. <br>3. migratedRows denotes the total number of migrated rows.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| SingleTableTolerance | Tables that are tolerant during the full synchronization phase. Each field is defined as follows: <br>1. objName indicates the name of the target table. <br>2. errorRows indicates the number of rows with tolerance. <br>3. status indicates the status of the table, which can be one of five states: <br>    \- NORMAL indicates that the table is normal and has no error rows. <br>    \- HAS_ERROR_ROWS_BUT_TASK_RUNNING indicates that there are error rows in the table, but they have not yet reached the tolerance threshold and the table continues full data synchronization. <br>    \- HAS_ERROR_ROWS_BUT_TASK_END indicates that there are error rows in the table and the table has completed full data synchronization. <br>    \- EXCEED_FAULT_TOLERANCE_THRESHOLD indicates that the number of tolerant error rows has exceeded the tolerance threshold, causing the table to stop full data synchronization. <br>    \- FAILED indicates that the full data synchronization of this table has failed for unknown reasons. <br>4. errorMsg stores error information, up to ten error messages (the first nine and the last one). <br>5. latestErrorMsg indicates the last error message. |

**Incremental Data Synchronization Metrics**

Incremental data synchronization metrics are used to monitor the state and real-time performance of tasks in the incremental data synchronization phase. The metric content is as follows:

|Field |Description |
| ------------------------- | ------------------------------------------------------------ |
| SourceType                   | Type of the source connector.                                |
| SourceVersion                | Version of the source connector.                             |
| SinkType                     | Type of the target connector.                                |
| SinkVersion                  | Version of the target connector.                             |
| StartTime                    | Start time of incremental data synchronization.              |
| Total(MB)                   | Amount of data synchronized.                                 |
| Throughput(KB/s)            | Data throughput.                                            |
| RPS                          | Number of rows synchronized per second.                     |
| TPS                          | Number of transactions synchronized per second.             |
| Delay                        | Latency from the source connector landing to the target connector landing. |
| ProcessDelay                 | Latency produced by DMC processing.                          |
| ReceiveLcrCount              | Number of logical log entries received by the source connector. |
| LastReceiveLcr               | The last logical log entry received by the source connector. |
| DmlWriteCount                | Number of DML entries written by the target connector.      |
| DdlWriteCount                | Number of DDL entries written by the target connector.      |
| TransactionCount             | Total number of transactions synchronized by the target connector. |
| LastWriteEvent               | The last event written by the target connector.              |
| LastTransactionEventCount    | Number of LCR entries in the last received transaction.      |
| LastTransactionPartCount     | The concurrency level of the last received transaction (transaction-consistent&#124;transaction-serialize). |
| UpdatePrimaryKeyCount        | Number of rows updated with primary keys written by the target connector (data-consistent mode). |
| NonPrimaryKeyCount           | Number of rows written by the target connector for tables without primary keys. |
| SourceReceivePercentage      | Estimated time consumption of data reception by the source connector. |
| SourceCollectPercentage      | Estimated time consumption of data transmission by the source connector. |
| SinkReceivePercentage        | Estimated time consumption of data reception by the target connector for each concurrency. |
| SinkProcessPercentage        | Estimated time consumption of data transmission by the target connector for each concurrency. |
| IsRecover                    | Indicates whether the target connector is currently in the recovery phase for each concurrency. |
| lastEventLag                 | Latency information and time point (indicates the time point of the latest source data). |
| lag                          | Latency information and time point (indicates that the source data prior to this time point has landed on the target). |
| lagVersion                   | Version of the heartbeat checkpoint and whether it is triggered by the heartbeat checkpoint (e.g., 1 HeartbeatTriggered). |

> **Note**:
>
> Tasks resumed through breakpoints will be considered as new tasks, and previous metric data will not be retained.

#### show parameter [\<key>]

The show parameters command is used to display various parameter information for DMC components.

Parameter Description:

- key: Displays information for a specified parameter. If not specified, it shows information for all parameters as follows:

|Key |Description |
| ----------------- | ------------------------------------ |
| auditLogLevel     | Current log level for audit logs.                           |
| bindPort          | Current port number of the DMC server connection.           |
| executeRunTimeout | Current timeout for tasks submitted by the run command.     |
| instanceName      | Current name of the connected DMC server instance.         |
| javaHome          | Current JAVA_HOME environment variable that the DMC service depends on. |
| logLevel          | Current log level for task logs.                            |

#### show status [--detail]

The show status command is used to display the current status information of the task. Status information includes:

- task: Task name.
- stage: Current stage of the task.
- state: Current state of the task's current stage.
- error message: Display error information when the task fails.
- task config: The configuration file used for this task.

Parameter Description:

- --detail: When content is excessive, only part is displayed by default. Specifying this parameter shows the full content.

#### show version

The show version command is used to display the version information of DMC corresponding to the current client.

#### show sql-trace [--detail]

show sql-trace is used to locate the SQL execution time during program running. Information includes:

- scope: Where the connection is executed
- traceId: Internal field, Trace ID, unique record tracker
- registrationThread: Name of the thread registering the tracker
- sql: Current SQL being executed
- startTime: Execution start time
- cost: Execution cost, unit ms
- connStatus: Connection status
  - EXEC_QUERY: Execute query SQL statement
  - EXEC: Execute SQL statement
  - EXEC_CALL: Execute stored procedure SQL statement
  - FETCH: Get data
  - BIND: Bind parameters
  - ADD_BATCH: Add batch parameters
  - CLOSE: Close connection
  - COMMIT: Commit
  - ROLLBACK: Rollback

Parameter Description:

- --detail: When content is excessive, only part is displayed by default. Specifying this parameter shows the full content.

#### shutdown [--force]

The shutdown command is used to issue a stop service instruction to the DMC server.

Parameter Description:

- --force: Ignores running tasks and forcibly shuts down the service.

#### startup [--config-path \<yds\_config>]

The startup command is used to issue a start service instruction to the DMC server.

Parameter Description:

- --config-path: Specifies the configuration file path used to start the service. If this parameter is omitted, it will use the configuration file under the DMC installation directory where the current client is located.

#### stop [--normal] [--no-wait]

The stop command is used to issue a task termination instruction to the DMC server.

Parameter Description:

- --normal: This parameter is used to ensure transaction consistency when terminating tasks in data consistency mode. If the task is in the middle of executing a large transaction, waiting for the large transaction to complete before terminating the task may cause the termination command to fail. Therefore, use this parameter cautiously during synchronization of large transactions. If you need to ensure instantaneous consistency for the transaction, it is recommended to use transactional consistency mode for data synchronization.
- --no-wait: Specifying this parameter indicates that the client will send the task termination command to the server and return immediately without waiting for the task to finish.

## WEB API

DMC provides the following privilege-restricted interfaces based on the HTTP protocol for quick integration by other tools. It also provides an API toolkit written in JDK1.8 to facilitate quick access to the API formats and DTO class templates.

### flashback

GET type API used to query the most recently recorded flashback point.

#### Request URL

http://*host:port*/yas-data-sync/api/flashback

Where host and port refer to the IP and service port of the DMC server (default installation is 8100).

#### Request Parameters

This API call does not require parameters.

#### Response Body

- Name: FlashbackDTO
- Structure: JSON format, including the most recently recorded source database flashback point and the most recently recorded target database flashback point, among other information.

**Example**

```json
{
  "sourceType": "YashanDB",   
  "sinkType": "Oracle",      
  "sourceFlashback": "677045096679743488",         
  "sinkFlashback": "20530359",					
  "startSamplingTime": "2025-03-17 16:23:00.001",    
  "endSamplingTime": "2025-03-17 16:24:00.003",		
  "version": ""                    
}
```

### task/status

GET type API used to query task status information.

#### Request URL

http://*host:port*/yas-data-sync/api/task/status

#### Request Parameters

This API call does not require parameters.

#### Response Body

- Name: TaskDetailDTO
- Structure: JSON format.

**Example**

```json
{
  "taskName": "simple_task",
  "taskState": {
    "taskStage": "INCREMENT",
    "state": "RUNNING",
    "errorMessage": ""
  },
  "taskConfigPath": "C:\\Users\\program\\IdeaProjects\\yas-data-sync\\run\\conf\\task_config.yml",
  "messages": [
    "INFO: Checking if current user is CDB user...",
    "INFO: CDB user check pass.",
    "INFO: Checking if ARCHIVELOG mode is enabled...",
    "INFO: ARCHIVELOG mode check pass.",
    "INFO: Checking if supplemental log is enabled...",
    "INFO: Supplemental log check pass.",
    "INFO: Checking if SYS.SEQ$ supplemental log is enabled...",
    "INFO: SYS.SEQ$ supplemental check pass.",
    "INFO: Querying source tables exist...",
    "INFO: Querying source tables exist pass."
  ]
}
```

### show-metadata-metrics

GET type API used to query metric information for the metadata synchronization phase.

#### Request URL

http://*host:port*/yas-data-sync/api/show-metadata-metrics

Where host and port refer to the IP and service port of the DMC server (default installation is 8100).

#### Request Parameters

This API call does not require parameters.

#### Response Body

- Name: MetadataMetricsDTO
- Structure: JSON format, including query time and various metrics data.

**Example**

```json
{
  "startTime": "2025-03-17 16:23:00.001",
  "endTime": "2025-03-17 16:24:00.003",
  "totalQueryTables": 0,
  "totalMigratedTables": 0,
  "totalValidateTables": 0,
  "queryTablesThroughput": 0,
  "migratedTablesThroughput": 0,
  "validateTablesThroughput": 0,
  "tableMetadataToleranceMetrics": [
    {
      "sourceTable": "tolerance.column_same_tab2",
      "sinkTable": "tolerance.column_same_tab2",
      "toleranceMsg": "Source table(tolerance.column_same_tab2) doesn't have a column name corresponding to sink table(tolerance.column_same_tab2) column(id03)",
      "metadataStage": "METADATA_CHECK"
    }
  ]
}
```

### show-snapshot-metrics

GET type API used to query metric information for the full data synchronization phase.

#### Request URL

http://*host:port*/yas-data-sync/api/show-snapshot-metrics

Where host and port refer to the IP and service port of the DMC server (default installation is 8100).

#### Request Parameters

This API call does not require parameters.

#### Response Body

- Name: SnapshotMetricsDTO
- Structure: JSON format, including query time and various metrics data.

**Example**

```json
{
  "startTime": "2025-03-17 16:23:00.001",
  "endTime": "2025-03-17 16:24:00.003",
  "migratingTables": [],
  "migratedTables": 1,
  "totalTables": 1,
  "migratedByteSize": "154.41 KB",
  "migratedRows": 10024,
  "migrateByteSpeed": "11.88 KB/s",
  "migrateRowsSpeed": "771 r/s",
  "singleTableMigrateMetrics": {
      "schema.table":{
          "objName": "schema.table",
          "migratedBytes": 0,
          "migratedRows": 0
        }
  },
  "singleTableToleranceMetrics": {
      "schema.table":{
          "objName": "schema.table",
          "errorRows": 200,
          "status": "EXCEED_FAULT_TOLERANCE_THRESHOLD",
          "errorMsg": "YAS-04008 id01 size exceeding limit 20",
          "latestErrorMsg": "YAS-04008 id01 size exceeding limit 20"
        }
  }
}
```

### show-increment-metrics

GET type API used to query incremental synchronization metrics information.

#### Request URL

http://*host:port*/yas-data-sync/api/show-increment-metrics

#### Request Parameters

This API call does not require parameters.

#### Response Body

- Name: IncrementMetricsDTO
- Structure: JSON format.

**Example**

```json
{
    "startTime": "2025-11-20 14:50:50",
    "total": 0.0,
    "throughput": 0.0,
    "rps": 0,
    "tps": 0,
    "delay": "0.0ms",
    "processDelay": "0.0ms",
    "receiveLcrCount": 0,
    "lastReceiveLcr": "",
    "dmlWriteCount": 0,
    "ddlWriteCount": 0,
    "transactionCount": 0,
    "lastWriteEvent": "",
    "lastTransactionEventCount": 0,
    "lastTransactionPartCount": null,
    "updatePrimaryKeyCount": 0,
    "nonPrimaryKeyCount": 0,
    "sourceReceivePercentage": "0.0%",
    "sourceCollectPercentage": "0.0%",
    "sinkReceivePercentage": {
        "0": "0.0%",
        "1": "0.0%",
        "2": "0.0%",
        "3": "0.0%"
    },
    "sinkProcessPercentage": {
        "0": "0.0%",
        "1": "0.0%",
        "2": "0.0%",
        "3": "0.0%"
    },
    "recoverState": {},
    "lastEventLag": "0ms ",
    "lag": "0ms  (Please enable options.heartbeatEnabled to ensure regular refresh.)",
    "lagVersion": "0 NonHeartbeatTriggered"
}
```

### shutdown

POST type API used to close the entire migration task process.

#### Request URL

http://*host:port*/yas-data-sync/api/shutdown

Where host and port refer to the IP and service port of the DMC server (default installation is 8100).

#### Request Parameters

- Name: ShutdownReq
- Type: JSON

```json
{
  "force": true   // Indicates whether to force the closure of the task
}
```

#### Response Body

This API call has no response body.

### run

POST type API used to start a migration task.

#### Request URL

http://*host:port*/yas-data-sync/api/run

Where host and port refer to the IP and service port of the DMC server (default installation is 8100).

#### Request Parameters

- Name: TaskConfig
- Type: JSON

For details on the task configuration information, please refer to [Configuration Files](Configuration Files).

```json
{
  "taskName": "simple_task",   // Task name
  "source": {  // Source configuration
    "type": "Oracle",   
    "dataSource": {
      "hostname": [
        {
          "host": "127.0.0.1",
          "port": "1521"
        }
      ],
      "username": "C##YDS_ORACLE",
      "password": "HwfydolzxogxdRxTEmRQyrEq54NceA==",
      "database": "ORCLCDB",
      "linkType": "serviceName",
      "pdbName": "ORCLPDB",
      "pool": {
        "maximumPoolSize": "260",
        "minimumIdle": "10",
        "connectionTimeoutMs": "60000"
      }
    },
    "options": {
      "fetchSize": 200,
      "metadataParallelism": "8"
    }
  },
  "sink": {   // Target configuration
    "type": "Oracle",
    "dataSource": {
      "hostname": [
        {
          "host": "127.0.0.1",
          "port": "1521"
        }
      ],
      "username": "C##YDS_ORACLE",
      "password": "HwfydolzxogxdRxTEmRQyrEq54NceA==",
      "database": "ORCLCDB",
      "linkType": "serviceName",
      "pdbName": "ORCLPDB"
    },
    "options": {
      "snapshotBatchRows": 10000,
      "metadataParallelism": "4"
    }
  },
  "options": {    
    "syncMode": "INCREMENT",
    "conflictStrategy": "IGNORE"
  },
  "syncRange": {
    "schemas": [
      {
        "schema": "TEST",
        "mapName": "YDS"
      }
    ]
  },
  "configPath": null
}
```

#### Response Body

- Name: RunTaskDTO
- Structure: JSON format, including result data of the startup.

**Example**

```json
{
  "code": 0,
  "message": "success",
  "version": 0,
  "data": null
}
```

### stop

POST type API used to stop a migration task.

#### Request URL

http://*host:port*/yas-data-sync/api/stop

Where host and port refer to the IP and service port of the DMC server (default installation is 8100).

#### Request Parameters

- Name: StopTaskReq
- Type: JSON

```json
{
  "stopMode": "NORMAL"   // Ensures transaction consistency when terminating the task in data consistency mode
}
```

#### Response Body

This API call has no response body.

### resume

POST type API used to resume a migration task.

#### Request URL

http://*host:port*/yas-data-sync/api/resume

Where host and port refer to the IP and service port of the DMC server (default installation is 8100).

#### Request Parameters

- Name: ResumeWithConfigReq
- Type: JSON

For details on the task configuration information, please refer to [Configuration Files](Configuration Files).

```json
{
  "config": {
    "taskName": "simple_task",
    "source": {
      "type": "Oracle",
      "dataSource": {
        "hostname": [
          {
            "host": "127.0.0.1",
            "port": "1521"
          }
        ],
        "username": "C##YDS_ORACLE",
        "password": "HwfydolzxogxdRxTEmRQyrEq54NceA==",
        "database": "ORCLCDB",
        "linkType": "serviceName",
        "pdbName": "ORCLPDB",
        "pool": {
          "maximumPoolSize": "260",
          "minimumIdle": "10",
          "connectionTimeoutMs": "60000"
        }
      },
      "options": {
        "fetchSize": 200,
        "metadataParallelism": "8"
      }
    },
    "sink": {
      "type": "Oracle",
      "dataSource": {
        "hostname": [
          {
            "host": "127.0.0.1",
            "port": "1521"
          }
        ],
        "username": "C##YDS_ORACLE",
        "password": "HwfydolzxogxdRxTEmRQyrEq54NceA==",
        "database": "ORCLCDB",
        "linkType": "serviceName",
        "pdbName": "ORCLPDB"
      },
      "options": {
        "snapshotBatchRows": 10000,
        "metadataParallelism": "4",
        "incrementParallelism":  4
      }
    },
    "options": {
      "syncMode": "INCREMENT",
      "conflictStrategy": "IGNORE"
    },
    "syncRange": {
      "schemas": [
        {
          "schema": "LMD",
          "mapName": "YDS"
        }
      ]
    },
    "configPath": null
  },
  "ignoreFirstDdl": true
}
```

#### Response Body

- Name: ResumeDTO
- Structure: JSON format, including result data of the startup.

**Example**

```json
{
  "code": 0,
  "message": "success",
  "version": 0,
  "data": {
    "warns": []
  }
}
```

## JAVA API

After loading the jar packages in the lib folder of the DMC installation directory, methods and interfaces provided by DMC can be called in Java application projects.

### startup Method

This method is used to start the migration task process, including initializing synchronization service configuration, starting core thread pools, and executing predefined pre-startup callback logic, etc.

#### Parameters

|Parameter Type |Parameter Name |Parameter Description |
| :--------------------: | :------------------------------: | :----------------------------------------------: |
| ServerConfig (POJO)  | config                 | Global configuration object for the synchronization service, containing parameters such as port, timeout, etc. |
| boolean              | debug                  | Whether to enable debug mode (default false). |
| String               | ydsHome                | DMC server installation directory (must ensure the directory is readable and writable). |
| String               | javaHome               | Path to the JDK installation for the DMC server (must point to a valid JDK directory). |
| AspectInterface      | startupBeforeAssembleStartupExec | Pre-start callback interface (optional, for custom initialization logic). |

#### Return Value

- true: Service started successfully.
- false: Service failed to start (debug to investigate the cause).

#### Calling Example

```java
// 1. Create config object
ServerConfig config = ServerConfig.builder()
        .instanceName("my-server")
        .port("8100")  // Set server port
        .jvm(List.of("-XX:+HeapDumpOnOutOfMemoryError",
                "-Xms4g",
                "-Xmx4g",
                "-XX:+UseZGC"))
        .options(new Options("GMT+8:00"))
        .build();

// 2. Implement custom callback logic
AspectInterface callback = new AspectInterface() {
    @Override
    public void beforeAssemble(ServerConfig config) {
        System.out.println("Performing pre-startup checks...");
        // Custom logic (e.g., environment variable checks)
    }
    @Override
	public void debugStartExec(boolean debug) {
	}

	@Override
	public void notSupportOsExec() {
		System.out.println("Unsupported OS");
	}
	
	@Override
	public void handleConfigSerializationError(JsonProcessingException e) {
		System.out.println("handleConfigSerializationError ");
	}

	@Override
	public void pullCheckServerAliveExec(Process process) {
		if (process.isAlive()){
			System.out.println("process is alive ");
		}else {
			System.out.println("process is dead ");
		}
	}
};

// 3. Call startup method
boolean success = StartupUtil.startup(
    config,                 // Configuration object
    false,                  // Disable debug mode
    "/opt/yds",             // DMC server installation directory (replace with actual path)
    "/usr/lib/jvm/java-11"  // JDK installation path (replace with actual path)
);

// 4. Handle execution result
if (success) {
    System.out.println("Service started successfully.");
} else {
    System.err.println("Failed to start service.");
}
```
