##### Explanation of Potential Data Overflow Risks in Float/Float Unsigned Boundary Value Synchronization

Float/Float unsigned data only guarantees 6 significant digits of precision. If the synchronized data reaches or approaches the boundary values of this data type, data may exceed the boundary values due to the base mechanism, which may result in a risk of data synchronization overflow.

##### Explanation of Potential Data Overflow Risks in Timestamp Data Synchronization

The Timestamp data type in YashanDB (MySQL mode) has a range of 1-1-1 00:00:00.000000 to 9999-12-31 23:59:59.999999, while MySQL only supports the range from 1970-01-01 00:00:01 to 2038-01-19 03:14:07. Please ensure that the data inserted from the source is within the range supported by MySQL.

##### Explanation of Range Issues in Varbinary Data Type Synchronization

For the Varbinary data type, YashanDB (MySQL mode) supports a range of 0 to 65534, while the maximum row byte length for MySQL table creation does not exceed 65532. If an error "42000 Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs" occurs during synchronization, please check if the source table structure exceeds MySQL's byte length limit.

##### Explanation of Range Issues in Synchronization of BLOB, MEDIUMBLOB, LONGBLOB, TEXT, MEDIUMTEXT, LONGTEXT Data Types

For BLOB, MEDIUMBLOB, LONGBLOB, TEXT, MEDIUMTEXT, LONGTEXT types, the limit is set by the "max_allowed_packet" parameter. YashanDB (MySQL mode) can support up to 4GB, while writing a single row data exceeding 512MB to 1GB or exceeding the MySQL parameter `max_allowed_packet/2` poses an overflow risk.

##### Explanation of Bidirectional Synchronization Regarding the ZEROFILL Attribute

YashanDB (MySQL mode) columns do not support the ZEROFILL attribute. Unlike MySQL, it does not generate leading zeros, but this does not affect the bidirectional synchronization of the same data types with the ZEROFILL attribute. ZEROFILL is essentially a display format rather than a data storage method. The display characteristic adds leading zeros during client formatting. The storage nature of YashanDB (MySQL mode) and MySQL databases is fundamentally consistent, as the actual storage is binary numeric values.

##### Explanation of Potential Data Overflow Risks When Synchronizing SMALLINT to YEAR Types

The range for SMALLINT type is -32768 to 32767, and the range for YEAR type is 1901 to 2155, and 0000. Exceeding this range (for example, 2156) will cause insertion failure or errors.

##### Explanation of Synchronization Range Issues for BIGINT UNSIGNED Data Type as Autoincrement Primary Key

For the BIGINT UNSIGNED data type as an autoincrement primary key in YashanDB (MySQL mode), the value range is 1 to 18446744073709551614, while in MySQL, the value range is 1 to 18446744073709551613. There is a slight difference in the maximum values, and exceeding this maximum value will lead to insertion failure or errors.

##### Explanation of Timestamp Synchronization During Daylight Saving Time. For timestamps like '1991-04-14 02:05:31', it may report the error 'Data truncation: Incorrect datetime value: '1991-04-14 02:05:31'.

Root Causes of the Error:

1. Timestamp Time Range Limitation

   - The timestamp type only supports time ranges from `1970-01-01 08:00:01` to `2038-01-19 11:14:07` (UTC time).

   - If the inserted time exceeds this range (for example, earlier than `1970-01-01 08:00:01`), an error will be triggered.

   `1990-04-15 02:05:31` may seem within the range, but time zone conversion may cause the actual stored value to exceed the boundary.

2. Ambiguity in CST Time Zone Causing Time Offset

   MySQL's system_time_zone=CST is ambiguous: Java/JDBC interprets CST as Central Standard Time (UTC-6), while China Standard Time is actually UTC+8.

   Time zone conversion during writing: When inserting `1990-04-15 02:05:31.0` (client time), MySQL converts it to UTC time for storage. If misidentified as Central Standard Time (CST=UTC-6), the conversion to UTC results in `1990-04-15 08:05:31` (still within range); if identified as China time (CST=UTC+8), the conversion yields `1990-04-14 18:05:31` (also within range); however, if the server's time zone configuration is chaotic, the converted time may precede `1970-01-01` (for example, data from 1969), leading to out-of-bounds errors.

Mitigation Solutions:

1. Change the column data type to Datetime

   The advantage of datetime: supports a wider time range (1000-01-01 00:00:00 to 9999-12-31 23:59:59); does not rely on time zone conversion, directly stores the original time value.

2. Explicitly set MySQL time zone to UTC+8

   Resolving time zone ambiguity: Configure to explicitly specify the time zone to avoid erroneous CST parsing.

   Steps of operation: `SET global time_zone = '+08:00';`.

##### Explanation of Issues When Target Table Does Not Support Zero Month or Day in DATE Type

Please first modify SQL_MODE to ensure that the value of NO_ZERO_IN_DATE is TRUE, and ensure that the source data is compatible with the specifications of the target side to avoid synchronization failures.

##### OOM Issues When Migrating BLOB/CLOB Data During Full Synchronization

Solutions:

1. Refer to the pre-migration preparation guide to adjust the max_allowed_packet variable in YashanDB to the maximum value.
2. Ensure that the heap memory allocated to each thread is not less than 1.5GB, reduce the concurrency of the LOB table (source.options.connectorParameter.snapshotLobParallelism), or increase the JVM memory configuration in ${YDS_HOME}/conf/yds_config.yml. Recommended memory size: (Maximum LOB column size * 2 + 0.5GB) * snapshotLobParallelism.

##### Explanation of YStream Handling Large Transactions, DMC Execution Timeout on Remove Command

The reason is that YStream's deletion requires analysis and handling of large transactions. Please be patient, and you can also refer to the relevant documentation for YStream to query related views for assistance.

##### Explanation of Data Synchronization Failure Due to Default Value of Timestamp Column as On Update Current Timestamp in Manually Created Target Tables

The timestamp column in the MySQL target table defaults to on update current_timestamp. During data synchronization, update DML operations may fail to ensure that the target data is consistent with the source data due to the triggering mechanism of on update current_timestamp, leading to subsequent data synchronization mismatches. To avoid this data synchronization anomaly, please remove the default value of on update current_timestamp from the target timestamp column.

##### Explanation of Primary Key Conflict When Using Upsert Synchronization with Timestamp Column as Primary Key

The timestamp column in the MySQL target table defaults to on update current_timestamp, causing primary key conflicts during upsert due to the triggering mechanism of on update current_timestamp. To avoid this situation, please remove the default value of on update current_timestamp from the target timestamp column or set a default value for the primary key field.

Enable upsert synchronization by setting options.upsert.snapshotUseUpsert=true.

##### When recovering from a network failure, the recovery task reports an error YAS-02731 YStream server status is invalid, current status: RUNNING

Wait a while.