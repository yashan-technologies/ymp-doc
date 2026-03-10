##### The target side does not support DATE type with zero values for month or day

Please modify SQL_MODE to ensure that NO_ZERO_IN_DATE is set to TRUE, and ensure that the existing data on the source side is compatible with the target side specifications to avoid synchronization failures.

##### Out of memory (OOM) when migrating BLOB/CLOB type data during full sync

Solution:

1. Adjust the max_allowed_packet parameter of MySQL to the maximum value, referring to the migration preparation guide.
2. Ensure that the heap memory allocated for each thread is at least 1.5GB, you can reduce the concurrency of LOB tables (source.options.connectorParameter.snapshotLobParallelism), or increase the JVM memory configuration in ${YDS_HOME}/conf/yds_config.yml. The recommended memory size is: (maximum LOB column size * 2 + 0.5GB) * snapshotLobParallelism.

##### Error "log event entry exceeded max\_allowed\_packet; Increase max\_allowed\_packet on master"

Please increase the configurations of MySQL's slave_max_allowed_packet and max_allowed_packet, and slave_max_allowed_packet must be greater than max_allowed_packet.

##### YDS-0103 Executor exception

If interceptFloatSync is true (default is true), DMC will intercept tables that contain FLOAT and DOUBLE types in the task, and information about the intercepted tables will be logged in the warning log ($YDS_HOME/logs/YDS_INSTANCE/task_name/warn/xxx.log), resulting in the final task failure and corresponding exception information being thrown.

Solution:

Option 1: Set interceptFloatSync to false to allow synchronization of tables that contain FLOAT and DOUBLE types, but this may lead to inconsistency in FLOAT and DOUBLE type data; or set convertFloatToClientFormat to true, so that floating-point numbers during the incremental phase are output in MySQL client format, ensuring that full data can be successfully updated or deleted during the incremental phase, but this may cause some loss of precision.

Option 2: Identify which tables contain FLOAT and DOUBLE type data by reviewing the warning log, change the data types of these fields to DECIMAL type, and then restart the task to synchronize.

Option 3: Identify which tables contain FLOAT and DOUBLE type data by reviewing the warning log, set a blacklist for the task to exclude these tables, and then restart the task to synchronize.

##### MySQL table includes ENUM or SET type fields with complex characters such as emojis, causing data garbled during incremental data synchronization

Incremental data is obtained through the MySQL binlog connector, which can only capture the index values of ENUM or SET. The actual values come from the COLUMN_TYPE values of MySQL's information.columns table, and the index values obtained during the incremental process are used to get the actual values. MySQL 5.7.x uses utf8_mb3 character set as the default encoding for the information.columns table, which cannot encode complex characters like emojis, resulting in garbled storage of specific values in information.columns.COLUMN_TYPE for ENUM or SET data types. Please ensure that the data queried from the COLUMN_TYPE column of this table does not get garbled, otherwise, there will be a risk of data inconsistency during ENUM or SET data synchronization.

##### Adding FLOAT or DOUBLE columns with default values during the incremental phase results in inconsistent default generated data between source and target, leading to failures in subsequent UPDATE/DELETE operations

**Problem Cause**: The default value `876.4265` may be stored as different approximate values (such as 876.427 or 876.4265) on the source and target sides, leading to data inconsistency. This precision difference is particularly noticeable during cross-database synchronization, as different storage engines or databases may have different rules for handling floating-point numbers.

**Impact Scenario**: Tables without primary keys or primary keys of floating point types.

**Mitigation Plan**: Use precise numeric types instead; DECIMAL is recommended, as DECIMAL stores precise numbers as strings, avoiding floating-point precision loss.

##### BINARY/VARBINARY type to RAW type mapping issues

**1. Length definition compatibility issue**

- MySQL allows defining `BINARY(0)` or `VARBINARY(0)` columns.
- YashanDB does not support RAW(0) definition. If synchronization includes zero-length columns, table creation will fail.
- The RAW type length range for YashanDB versions below 23.3 is 1-8000, while the varbinary type length range for MySQL is 0-65535.
- Mitigation plan:
  - Check the source table structure before synchronization and change zero-length BINARY/VARBINARY columns in MySQL to the minimum length (e.g., BINARY(1)) or replace them with other compatible types (e.g., CHAR(1)).
  - For YashanDB versions below 23.3, the RAW type cannot cover the length range of varbinary types; please use BLOB type.

**2. Primary key constraint support differences**

- MySQL's BINARY/VARBINARY columns support creating primary key constraints.
- YashanDB's RAW type cannot be used as a primary key column. 
- Mitigation plan:
  - **Option 1**: Remove the primary key constraint on the binary column from the source table and use another indexed column (such as auto-increment ID) as the primary key.
  - **Option 2**: Convert the binary column to an index type supported by YashanDB (such as `VARCHAR`).

**3. Behavior of inserting data into these two data type columns through JDBC**

- MySQL's BINARY/VARBINARY columns insert empty byte arrays through JDBC, resulting in empty strings.
- YashanDB's RAW type inserts empty byte arrays through JDBC, resulting in NULL. 
- Impact scenarios:
  - Synchronization of empty string data poses a risk of data inconsistency.
  - Updates and deletes on tables without primary keys.
- Mitigation plan:
  - **Option 1**: Do not insert empty strings into MySQL's BINARY/VARBINARY columns.
  - **Option 2**: Replace with other data types.

**4. Byte data length support range of YashanDB JDBC in where conditions**

- Problem manifestation: The length of byte data in where conditions supported by YashanDB JDBC cannot exceed 32000.
- Impact scenario:
  - For VARBINARY columns in tables without primary keys, lengths exceeding 32000 will cause subsequent update and delete events to fail data synchronization.
- Mitigation plan:
  - **Option 1**: Add a primary key to the table without a primary key.
  - **Option 2**: Convert the binary column to BLOB data type.

**5. Default value support differences**

- Problem Cause:
  - YashanDB fails to create tables due to byte encoding length issues with default values as empty strings.
  - YashanDB does not support default values other than numbers and English characters for RAW types.
- Impact scenarios:
  - Empty strings as default values.
  - Default values other than numbers and English characters.
  - The default value for BINARY automatically fills with \0, which can also lead to synchronization table creation failures.
- Mitigation plan:
  - **Option 1**: Modify the default value of VARBINARY columns; BINARY columns should not set default values.
  - **Option 2**: Change MySQL's binary columns to BLOB data type.

##### Occasional IOException exception in binlog client

DMC provides a way to retry the binlog client to avoid IOException exceptions.

**Parameter Name**: `source.options.connectorParameter.maxRecentRetriableCount`
**Applicable Scenario**: Controls the retry strategy of the Binlog Client in the event of an `IOException` exception.

**Parameter Value Range**:

1. Default value (-1): unlimited retries.
2. Value of 0: disables retries.
3. Value of 1 to 2<sup>31</sup>-1: maximum number of retries within one hour.

##### Synchronization error "Auxiliary database '$YDS\_AUX\_INFO' does not exist"

When the lower_case_table_names parameter of the source MySQL is set to 0, please check if the $YDS_AUX_INFO database exists on the source side, and you need to manually create this database, ensuring that the database name is uppercase.