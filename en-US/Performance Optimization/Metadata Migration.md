### Performance Optimization for Target Database Migration

YMP metadata migration will execute object creation DDL in the target database in parallel. The performance of metadata migration relies on the execution performance of the target database. For database configuration optimization, refer to the YashanDB Performance Optimization Manual.

### YMP Metadata Migration Tuning Configuration Parameters

|Parameter Name |Default Value |Parameter Description |  
|--------------|--------|---------------------------------------------------------------------------------------------------------------------------------| 
| Max Connections for Database Query  | 20            | The maximum number of threads for querying views on the source and target sides during the metadata migration process. To improve performance, multiple query threads will be started to query information needed for migration in batches. This parameter configures the number of parallel threads for querying on the source and target sides during metadata migration. |  
| Max Connections for Target Database Execution | 20 | The number of threads for creating objects on the target side during metadata migration. The objects will be executed in parallel on the target side to improve migration efficiency. This parameter configures the number of parallel threads for DDL execution on the target side during metadata migration. The total sum of Max Connections for Database Query and Max Connections for Target Database Execution is the final number of connections to the target database during the migration process. |  
| Automatically Add Parallel for Index Migration | true | Whether to use the parallel parameter when creating indexes. true/TRUE: use, false/FALSE: do not use. When true, parallelism takes effect. |  
| Max Connection Wait Time            | 300000        | The wait time for metadata migration thread tasks to acquire connections, in ms. The connection pool established during YMP metadata migration will automatically create database connections for task use according to the set parallelism (max(database query max connections, target database execution max connections) + 2). |  

Tuning reference explanations are as follows:

#### Max Connections for Database Query

This parameter is generally sufficient; however, during the migration of a large number of metadata objects, if the logs or stack indicate that tasks are in batch querying for too long and the database is not under significant pressure, consider increasing this parameter.

#### Max Connections for Target Database Execution

This parameter has the greatest impact on metadata migration performance. During tasks involving a large number of metadata object migrations, if the target database is not under significant pressure, consider increasing this parallelism to improve migration performance.

#### Automatically Add Parallel for Index Migration

When used, the PARALLEL parameter will be applied when creating indexes. For details on the effects of this parameter, refer to the YashanDB documentation regarding PARALLEL.

#### Max Connection Wait Time

If the database establishes connections slowly, consider increasing the wait time.