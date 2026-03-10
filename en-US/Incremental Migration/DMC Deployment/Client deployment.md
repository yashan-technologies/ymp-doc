The DMC component's client is currently deployed along with the server, requiring no separate installation and configuration.

For guidance on various operations for the client, please refer to the [DMC CLI Interface Introduction](../DMC Introduction/User Interfaces). This document only lists basic operational guidance such as starting and stopping.

## Starting the Client

After the server has been deployed, navigate to the DMC installation directory and execute the following command to start the corresponding client:

::: tabs

== Linux

```shell
# Interactive mode
$ ./bin/start_client.sh
```

== Windows

```shell
# Interactive mode
{DMC installation directory}\bin> start_client.bat
```

:::

## Running the Client

After starting the client in interactive mode, the client window remains active, allowing users to execute commands to start the server, initiate migration tasks, monitor migration tasks, and more.

### Starting the DMC Service

Execute the following commands in the client to verify connectivity with the server and start the server:

```shell
$ ping
$ startup
```

### Starting Migration Tasks

```shell
# Start migration tasks based on parameters in the server configuration file
$ run
```

## Exiting the Client

Execute the following command to exit the client started in interactive mode:

```shell
$ quit
```

### Stopping Migration Tasks

```shell
$ stop
```

### Stopping the DMC Service

Before exiting the client, you can choose whether to stop the connected server. The command to stop the service is as follows:

```shell
$ shutdown
```

Exiting the client without stopping the DMC service will not affect the operation of migration tasks, nor the communication with the server when the client is restarted.