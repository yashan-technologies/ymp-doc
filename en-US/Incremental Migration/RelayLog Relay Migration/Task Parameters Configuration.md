Before starting the migration task, a series of task parameter configurations must be completed first. DMC configures the parameters by directly editing the task configuration file. The default task configuration file is the task_config.yml file located in the conf directory.

All parameters supported by DMC and their configuration methods are listed in the [Configuration File Introduction](../DMC Introduction/Configuration Files) document.

### Basic Parameter Configuration

Basic parameter configuration involves making some necessary settings to meet the requirements for the task to start successfully. As shown below:

::: tabs

== Source End to RelayLog

```yaml
taskName: simple_task     # Change to the actual task name
source:
  type: YashanDB             # Change to the actual source type
  dataSource:    
    hostname:
      - host: 127.0.0.1    # Change to the actual YashanDB server address
        port: 1688         # Change to the actual YashanDB listener port
    username: dmc_user     # Change to the actual tool username
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==           # Fill in the string encrypted by encryptpwd
  options:
    connectorParameter:

sink:
  type: RelayLog            # The target type must be RelayLog
  dataSource:
    incrementParallelism: 1     #Incremental concurrency (currently only single-threaded concurrency is supported)
    connectorParameter:
      basePath: D:\project-source\yas-data-sync\run\tmp\YDS_INSTANCE\TESTFF11212132wqwq2  # Relay log file storage path
	  fileSizeThreshold: 104857600  # Threshold size of individual log file (in bytes); automatically switch files upon reaching the limit.   

options:
  syncMode: increment              # Must be incremental synchronization mode.
  conflictStrategy: override     # Change to the actual conflict strategy
  truncate: 'true'              # Whether to clear target data
syncRange:
  schemas:
    - schema: YDS                # Change to the actual Schema name
```

== RelayLog to Target End

```yaml
taskName: simple_task     # Change to the actual task name
source:
  type: RelayLog             # The source type must be RelayLog
  options:
    connectorParameter:
      basePath: D:\project-source\yas-data-sync\run\tmp\YDS_INSTANCE\TESTFF11212132wqwq2  # Relay log file storage path

sink:
  type: Oracle             # Change to the actual target type
   dataSource:
    hostname:
      - host: 127.0.0.1    # Change to the actual Oracle server address
        port: 1688         # Change to the actual Oracle listener port
    linkType: serviceName          # Connection type: serviceName
    database: ORCLPDB1          # Oracle database service name
    username: SYSTEM               # Database user name
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==           # Databae user password. Fill in the string encrypted by encryptpwd

options:
  syncMode: increment              # Must be incremental synchronization mode.
  conflictStrategy: override     # Change to the actual conflict strategy
  upsert:
	incrementUseUpsert: incrInsertUpdateUseUpsert  # For incremental insert/update operations, use upsert mode.
syncRange:
  schemas:
    - schema: YDS                # Change to the actual Schema name
```

:::

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

#### Synchronization Strategy Parameters

When the options.conflictStrategy parameter is configured as override, or when options.truncate is set to true, the metadata file, checkpoint file, index file, and log file under the basePath path will be deleted before each migration task.