### Verification of Source and Target Database Performance Optimization

YMP's full verification will stream data from both source and target for comparison. Performance depends on the parallel execution capabilities of the assessment database. Database configuration optimization should refer to various database performance optimization manuals.

### YMPData Validation Tuning Configuration Parameters

|Parameter Name |Default Value |Parameter Description |
|-----------------------------------------|----------|-----------------------------|
| checkTask.task.parallel.max-num           | 20            | The maximum number of parallel verification tasks allowed on the YMP platform. |
| checkTask.datasource.max-connection       | 500           | The maximum number of connections supported per data source for all verification tasks on the YMP platform. |
| checkTask.datasource.connection-timeout   | 10000         | The connection timeout for all verification tasks on the YMP platform, in milliseconds (ms). |
| Performance Configuration                  | 50            | Set the maximum number of tables that can be validated simultaneously. |
| Large Table Split Concurrency              | 10            |                                                   |
| Large Table Split Row Count                | 50000000      |                                                   |
| LOB Type Fetch Length                      | 1080          |                                                   |
| Inconsistent LOB Type Difference Display Length | 200       |                                                   |

The tuning reference explanations are as follows:

checkTask.task.parallel.max-num：

The maximum number of verification tasks allowed to be executed simultaneously on the YMP platform. Typically, this value will not exceed the default. If too many verification tasks are running concurrently, this configuration can be increased appropriately.

checkTask.datasource.max-connection：

The maximum number of connections that can be obtained from the connection pool used for verification tasks. This connection pool provides connections necessary for all verification tasks. If the number of connections does not meet the number of tables being verified simultaneously, the tasks cannot be initiated. This parameter is related to the database's own configuration. Please ensure this number is less than the maximum allowable connections for the data source.

checkTask.datasource.connection-timeout：

The timeout duration for obtaining connections from the connection pool used for verification. If the connection cannot be obtained within this timeframe, the task will encounter an error. Please configure this according to database performance.

Performance Configuration:

The number of tables that can be validated simultaneously for a single task. If the machine performance and database processing capabilities are favorable, the concurrency can be increased to improve performance. Ensure that the database can provide a sufficient number of connections and adequate sorting capability, and that the YMP runtime environment has sufficient memory.

Large Table Split Concurrency:

If the number of verification for a table exceeds the large table split row count, the table will be split for comparison based on the large table split row count. This concurrency represents the maximum number of sub-tasks that can be executed simultaneously after splitting a single table into smaller tasks. Refer to the notes regarding performance configuration type.

Large Table Split Row Count:

Determines whether a table falls into the category of a large table. If the number exceeds this value, the table will be split into sub-tasks for verification based on row count. This value is strongly related to machine and database performance. Be cautious of making it too small, as this will lead to excessive consumption and create too many database connections.

LOB Type Fetch Length:

The size of data fetched in one go for large object types. The blob type refers to the byte array size, and the clob type refers to the char array size. With sufficient memory allocated to YMP, this value can be increased appropriately to enhance stream retrieval speed.

Inconsistent LOB Type Difference Display Length:

The length of the difference display for large object types, composed of the offset where the difference occurs and the contents following the offset. This size will consume some machine memory and performance. When ensuring that differences can be identified, a smaller size is more beneficial for performance improvement.