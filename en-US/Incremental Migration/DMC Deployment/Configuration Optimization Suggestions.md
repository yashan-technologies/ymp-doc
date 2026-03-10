In a production environment, improving the performance of migration tasks includes three parts:

1. Source-side optimization: Enhancing the database's multi-session parallel capability and data throughput under the performance requirements of the source database itself will help improve the efficiency of data retrieval from the source during migration tasks.

2. Destination-side optimization: Enhancing the database's multi-session parallel capability and data throughput under the performance requirements of the destination database itself will help improve the efficiency of data insertion into the destination during migration tasks.

3. DMC component optimization: The configuration parameters in the configuration file constitute the core of DMC service operation. By adjusting appropriate parameter values, resource utilization can be optimized, overall efficiency can be improved, and the system can quickly and stably handle tasks while meeting demand.

This article will introduce and suggest key configuration items that affect DMC operation performance. Please choose based on actual conditions.

## Concurrency

The DMC server task configuration file ({DMC installation directory}/conf/task_config.yml) contains multiple concurrency-related configuration items. Please refer to the following suggestions to decide whether to modify the default configuration:

**Source Connector Concurrency**

The value of `source.options.metadataParallelism` specifies the number of threads for concurrent metadata queries by the source connector. Each thread will hold one connection to the source database.

When there are many tables to synchronize (for example, tens of thousands), increasing this value can enhance the speed of metadata queries.

**Destination Connector Concurrency**

The value of `sink.options.metadataParallelism` specifies the number of threads for concurrently executing DDL by the destination connector. Each thread will hold one connection to the destination database.

When there are many tables to synchronize (for example, tens of thousands), increasing this value can enhance the speed of DDL execution.

**Full Data Synchronization Concurrency**

The value of `options.snapshotParallelism` specifies the number of concurrent threads for performing full data synchronization.

As an I/O intensive task, it is recommended to adjust the concurrency level based on the hardware specifications of the server and the load conditions of both connectors. It is recommended that each concurrent thread monopolizes one CPU logical core to ensure optimal performance.

At the same time, it is suggested to perform full data synchronization when there is less business activity on the source side to minimize the impact on online business and achieve a better user experience.

> **Caution**:
>
> When adjusting the concurrency for full synchronization, please also adjust the heap size of the DMC service (see the next section description) to avoid task failures due to insufficient memory.

**Incremental Synchronization Concurrency**

The value of `sink.options.incrementParallelism` specifies the maximum concurrency available for the destination connector when executing DML. The concurrency principles differ between data consistency mode and transaction consistency mode. Adjusting this value appropriately will significantly improve the performance of incremental data synchronization.

## JVM Startup Parameters

Tuning the JVM startup parameters can customize the JVM runtime environment for different scenarios and requirements, maximizing performance, enhancing resource utilization, and ensuring application stability under high load, reducing performance fluctuations related to GC.

The JVM startup parameter configuration items are located in the DMC server task configuration file ({DMC installation directory}/conf/task_config.yml), with the default values as follows:

```yaml
jvm:
  - '-XX:+HeapDumpOnOutOfMemoryError'
  - '-Xms4g'
  - '-Xmx4g'
  - '-XX:+UseZGC'
```

**How to Choose the Right Garbage Collector**

Based on data measured using the TPC-H model, when the concurrency for full synchronization is less than 32, and the average maximum memory size allocated per concurrent thread is less than 2GB, it is recommended to use ZGC as the garbage collector. Otherwise, it is recommended to use G1 as the garbage collector.

**What is the Basis for JVM Heap Memory Size Configuration**

First, please ensure that the average maximum memory size allocated per concurrent thread is not less than 512MB.

Second, it is recommended to adjust the initial heap size (Xms) and maximum heap size (Xmx) to the same value in the production environment to ensure stable operation under high load.

## Connector Cursors

DMC buffers recently used connector cursor resources using the LRU algorithm. Holding more cursors means DMC can more quickly utilize buffered cursors to execute DML.

The `sink.options.incrementMaxUseCursors` in the DMC server task configuration file ({DMC installation directory}/conf/task_config.yml) limits the maximum number of cursors used by the destination connector during the incremental phase. Increasing this value can increase the number of cursors in DMC's buffer. Adjusting this value appropriately will greatly enhance the performance of incremental data synchronization.

>**Caution**:
>
> Setting this value inappropriately may lead to task failures during synchronization due to resource shortages.

## Consistency Mode

The consistency mode determines the data consistency assurance level between the source and destination during the incremental phase, configured through the `options.consistencyLevel` in the DMC server task configuration file ({DMC installation directory}/conf/task_config.yml). The different modes also have varying impacts on performance.

**Data Consistency Mode**

When the value of `options.consistencyLevel` is set to data-consistent, DMC will split the transaction information received by the source connector, perform hash mapping based on the table name, and allocate DML to each concurrently executing thread. This is beneficial for multi-table synchronization handling.

In data consistency mode, DMC also employs DML merging and bulk insert techniques, suitable for synchronization tasks in analytical scenarios and cases where there are significant transactions on the source side.

**Transaction Consistency Mode**

When the value of `options.consistencyLevel` is set to transaction-consistent or transaction-serialize, the group commit technique used during log writing on the source side naturally differentiates unrelated transactions, allowing DMC to handle these unrelated transactions concurrently during operation.

In this mode, the throughput of DMC is affected by the concurrency level of the source-side business and certain advanced parameters on the source side. Please refer to the product documentation for configuration suggestions for specific types of migration tasks.

>**Note**:
>
> In the task configuration file, when the value of `options.consistencyLevel` is set to transaction-serialize, DMC still utilizes the group commit technique on the source side for transaction concurrency, ensuring strict serial commits only before committing the transactions.

## Database Connection Pool

The `source.dataSource.pool.minimumIdle` and `sink.dataSource.pool.minimumIdle` in the DMC server task configuration file ({DMC installation directory}/conf/task_config.yml) define the minimum number of connections for the connection pool on the source and destination sides, respectively.

When the concurrency for full and incremental synchronization is high, it is recommended to configure the minimum connection for the database connection pool to the concurrency level to enhance synchronization performance.

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
