The DMC component is integrated into the YMP and is installed, upgraded, and uninstalled along with the YMP product. At the same time, the DMC component also supports standalone deployment. The operations described in this section follow the method of standalone deployment for the DMC component.

The DMC component is divided into two parts: server and client.

**DMC Client**

The client provides a CLI interface for users to interact with the server, including starting, monitoring, and initiating migration task commands.

The client can be started using the start_client command, specifying the server to communicate with.

Multiple clients can be deployed on one machine. Multiple clients can be started, or one client can be started multiple times to communicate with multiple servers; however, a client can only communicate with one server in the same CLI window.

**DMC Server**

The server is responsible for listening to and executing various task commands, as well as storing all configurations, logs, and intermediate results of migrations.

Multiple servers can be deployed on one machine by setting different listening ports in the configuration file.

A server can only configure and execute one migration task.

This section will introduce the deployment and related configuration of the DMC component using the example of deploying one server and one client. Users can deploy multiple sets of DMC according to their actual needs and resource capabilities.

After performing the following operations in order, you can begin starting the DMC component service and initiate your migration tasks:

1. Perform [Server deployment](Server deployment)
2. Perform [Security Configuration](Security Configuration)
3. Perform [Monitoring Configuration](Monitoring Configuration)
4. Refer to [Configuration Optimization Suggestions](Configuration Optimization Suggestions) to adjust configuration parameters
5. Perform [Client deployment](Client deployment)