### Database Version

#### YashanDB Source



Version: 23.2.7 and above(The multi-tenant mode is only supported for users on version v23.5 and above).

Deployment Form: Standalone, YAC

Table Type: Only supports HEAP tables, excluding system tables and temporary tables.

Supports synchronization of up to 10,000 Schemas or 1 million tables of data.

Schema support range:

|YashanDB Schema Type |Data Location |User Connection Location |METADATA Mode |SNAPSHOT Mode |INCREMENT Mode |INITIAL Mode |
| ----------------------------------------- | -------------------------- | ----------------------------------------- | ------------------------------ | ------------------------------ | -------------------------------- | ---------------------------- |
| Traditional Schema                        | Local Database             | CDB$ROOT                                  | ✅                              | ✅                              | ✅                                | ✅                            |
| Multitenant Schema                        | Specific PDB               | PDB                                       | ✅                              | ✅                              | ✅                                | ✅                            |
| Multitenant Schema                        | Specific PDB               | CDB$ROOT                                  | ❌                              | ❌                              | ❌                                | ❌                            |
| Multitenant Schema                        | Multiple PDBs              | -                                         | ❌                              | ❌                              | ❌                                | ❌                            |
| Multitenant Schema                        | CDB$ROOT                   | CDB$ROOT                                  | ✅                              | ✅                              | ✅                                | ✅                            |



#### YashanDB Target



Version: 23.2.7 and above(The multi-tenant mode is only supported for users on version v23.5 and above).

Deployment Form: Standalone, YAC

Table Type: Only supports HEAP tables, excluding system tables and temporary tables.



Schema support range:

|YashanDB Schema Type |Data Location |User Connection Location |METADATA Mode |SNAPSHOT Mode |INCREMENT Mode |INITIAL Mode |
| ----------------------------------------- | -------------------------- | ----------------------------------------- | ------------------------------ | ------------------------------ | -------------------------------- | ---------------------------- |
| Traditional Schema                        | Local Database             | CDB$ROOT                                  | ✅                              | ✅                              | ✅                                | ✅                            |
| Multitenant Schema                        | Specific PDB               | PDB                                       | ✅                              | ✅                              | ✅                                | ✅                            |
| Multitenant Schema                        | Specific PDB               | CDB$ROOT                                  | ❌                              | ❌                              | ❌                                | ❌                            |
| Multitenant Schema                        | Multiple PDBs              | -                                         | ❌                              | ❌                              | ❌                                | ❌                            |
| Multitenant Schema                        | CDB$ROOT                   | CDB$ROOT                                  | ✅                              | ✅                              | ✅                                | ✅                            |



### Character Set

YashanDB supports:

CHARACTER_SET: UTF-8, GBK, GB18030

NATIONAL_CHARACTER_SET: UTF16

The migration task does not perform conversion and synchronization of character sets. Users must ensure that the character set range of the target is compatible with that of the source.

### Data Types

To be compatible with various types of databases, DMC has defined a set of tool types for inter-conversion mapping between source and target data types and provides user-defined mapping functionality for conversion from tool types to target data types.

The table below lists the source table types supported by DMC for migration, as well as the default mapping relationship from the source to the tool and then to the target data types:

::: tabs

== Target USE_NATIVE_TYPE=TRUE

|YashanDB Data Type |Tool Type |YashanDB (Sink) Data Type |YashanDB (Sink) Compatible Type |
| ------------------------------------------------------------ | ----------------------------        | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BIT(size)                                                    | BIT(n)                              | BIT(size)                                                    | BIT(>=size),  NUMBER(P, S),  P - S >= ((size - 1) - (size - 1) / 10) / 3 + 1 |
| TINYINT (USE_NATIVE_TYPE=TRUE)                               | INT8                                | TINYINT                                                      | TINYINT, SMALLINT, INTEGER, BIGINT,  NUMBER(P, S), P - S >= 3 |
| TINYINT (USE_NATIVE_TYPE=FALSE)                              | NUMBER(38, 0)                       | NUMBER(38, 0)                                                | NUMBER(P, S),  P - S >= 38                                   |
| SMALLINT (USE_NATIVE_TYPE=TRUE)                              | INT16                               | SMALLINT                                                     | SMALLINT, INTEGER, BIGINT,  NUMBER(P, S), P - S >= 5         |
| SMALLINT (USE_NATIVE_TYPE=FALSE)                             | NUMBER(38, 0)                       | NUMBER(38, 0)                                                | NUMBER(P, S),  P - S >= 38                                   |
| INTEGER (USE_NATIVE_TYPE=TRUE)                               | INT32                               | INTEGER                                                      | INTEGER, BIGINT,  NUMBER(P, S), P - S >= 10                  |
| INTEGER (USE_NATIVE_TYPE=FALSE)                              | NUMBER(38, 0)                       | NUMBER(38, 0)                                                | NUMBER(P, S),  P - S >= 38                                   |
| BIGINT (USE_NATIVE_TYPE=TRUE)                                | INT64                               | BIGINT                                                       | BIGINT,  NUMBER(P, S), P - S >= 19                           |
| BIGINT (USE_NATIVE_TYPE=FALSE)                               | NUMBER(38, 0)                       | NUMBER(38, 0)                                                | NUMBER(P, S),  P - S >= 38                                   |
| FLOAT(p) / REAL(p) (USE_NATIVE_TYPE=TRUE)                    | FLOAT                               | BINARY_FLOAT                                                 | BINARY_FLOAT, DOUBLE                                         |
| FLOAT(p) / REAL(p) (USE_NATIVE_TYPE=FALSE)                   | ORACLE_FLOAT(p)                     | NUMBER                                                       | NUMBER(*)/NUMBER                                             |
| BINARY_FLOAT                                                 | BINARY_FLOAT                        | BINARY_FLOAT                                                 | BINARY_FLOAT, DOUBLE                                         |
| DOUBLE/BINARY_DOUBLE                                         | DOUBLE                              | DOUBLE                                                       | DOUBLE                                                       |
| NUMBER(P, S)                                                 | NUMBER(p,s)                         | NUMBER(P, S)                                                 | NUMBER(P1, S1),  P1 >= P, S1 >= S                            |
| NUMBER(*)/NUMBER                                             | NUMBER                              | NUMBER                                                       | NUMBER                                                       |
| CHAR(size [byte&#124;char])                                      | CHAR(n [byte&#124;char])            | CHAR(size [byte&#124;char])                       | CHAR(size [byte&#124;char])                       |
| VARCHAR(size [byte&#124;char])                               | VARCHAR(n [byte&#124;char])         | VARCHAR(size [byte&#124;char])                    | VARCHAR(>=size [byte&#124;char])                  |
| NCHAR(size)                                                  | NCHAR(n)                            | NCHAR(size)                                                  | NCHAR(size)                                                  |
| NVARCHAR(size)                                               | NVARCHAR(n)                         | NVARCHAR2(size)                                              | NVARCHAR(>=size)                                             |
| RAW(size)/UROWID(size)                                       | RAW(n)                              | RAW(size)                                                    | RAW(>= size),  UROWID(>= size)                               |
| BOOLEAN                                                      | BOOLEAN                             | BOOLEAN                                                      | BOOLEAN,  NUMBER(P, S), P - S >= 1                           |
| DATE                                                         | DATE                                | DATE                                                         | DATE, TIMESTAMP                                              |
| TIME                                                         | TIME                                | TIME                                                         | TIME                                                         |
| TIMESTAMP(precision)                                         | TIMESTAMP(6)                        | TIMESTAMP                                                    | TIMESTAMP                                                    |
| INTERVAL YEAR (year_precision) TO MONTH                      | INTERVAL_YEAR_TO_MONTH(y)           | INTERVAL YEAR (year_precision)  TO MONTH                     | INTERVAL YEAR (>= year_precision)  TO MONTH                  |
| INTERVAL DAY(day_precision) TO SECOND(fractional_seconds_precision) | INTERVAL_DAY_TO_SECOND(d, f) | INTERVAL DAY(day_precision)  TO SECOND(fractional_ seconds_precision) | INTERVAL DAY(>= day_precision)  TO SECOND(>= fractional_ seconds_precision) |
| CLOB                                                         | CLOB                                | CLOB                                                         | CLOB                                                         |
| NCLOB                                                        | NCLOB                               | NCLOB                                                        | NCLOB                                                        |
| BLOB                                                         | BLOB                                | BLOB                                                         | BLOB                                                         |

== Target USE_NATIVE_TYPE=FALSE

|YashanDB Data Type |Tool Type |YashanDB (Sink) Compatible Type |
| ------------------------------------------------------------ | ----------------------------        | ------------------------------------------------------------ |
| BIT(size)                                                    | BIT(n)                              | BIT(>=size),  NUMBER(P, S),  P - S >= ((size - 1) - (size - 1) / 10) / 3 + 1 |
| TINYINT (USE_NATIVE_TYPE=TRUE)                               | INT8                                | NUMBER(P, S),  P - S >= 3                                    |
| TINYINT (USE_NATIVE_TYPE=FALSE)                              | NUMBER(38, 0)                       | NUMBER(P, S),  P - S >= 38                                   |
| SMALLINT (USE_NATIVE_TYPE=TRUE)                              | INT16                               | NUMBER(P, S),  P - S >= 5                                    |
| SMALLINT (USE_NATIVE_TYPE=FALSE)                             | NUMBER(38, 0)                       | NUMBER(P, S),  P - S >= 38                                   |
| INTEGER (USE_NATIVE_TYPE=TRUE)                               | INT32                               | NUMBER(P, S),  P - S >= 10                                   |
| INTEGER (USE_NATIVE_TYPE=FALSE)                              | NUMBER(38, 0)                       | NUMBER(P, S),  P - S >= 38                                   |
| BIGINT (USE_NATIVE_TYPE=TRUE)                                | INT64                               | NUMBER(P, S),  P - S >= 19                                   |
| BIGINT (USE_NATIVE_TYPE=FALSE)                               | NUMBER(38, 0)                       | NUMBER(P, S),  P - S >= 38                                   |
| FLOAT(p) / REAL(p) (USE_NATIVE_TYPE=TRUE)                    | FLOAT                               | BINARY_FLOAT, DOUBLE                                         |
| FLOAT(p) / REAL(p) (USE_NATIVE_TYPE=FALSE)                   | ORACLE_FLOAT(p)                     | FLOAT(>= p)                                                  |
| BINARY_FLOAT                                                 | BINARY_FLOAT                        | BINARY_FLOAT, DOUBLE                                         |
| DOUBLE/BINARY_DOUBLE                                         | DOUBLE                              | DOUBLE                                                       |
| NUMBER(P, S)                                                 | NUMBER(p,s)                         | NUMBER(P1, S1),  P1 >= P, S1 >= S                            |
| NUMBER(*)/NUMBER                                             | NUMBER                              | NUMBER                                                       |
| CHAR(size [byte&#124;char])                                      | CHAR(n [byte&#124;char])            | CHAR(size [byte&#124;char])                       |
| VARCHAR(size [byte&#124;char])                               | VARCHAR(n [byte&#124;char])         | VARCHAR(>=size [byte&#124;char])                  |
| NCHAR(size)                                                  | NCHAR(n)                            | NCHAR(size)                                                  |
| NVARCHAR(size)                                               | NVARCHAR(n)                         | NVARCHAR(>=size)                                             |
| RAW(size)/UROWID(size)                                       | RAW(n)                              | RAW(>= size),  UROWID(>= size)                               |
| BOOLEAN                                                      | BOOLEAN                             | BOOLEAN,  NUMBER(P, S), P - S >= 1                           |
| DATE                                                         | DATE                                | DATE, TIMESTAMP                                              |
| TIME                                                         | TIME                                | TIME                                                         |
| TIMESTAMP(precision)                                         | TIMESTAMP(6)                        | TIMESTAMP                                                    |
| INTERVAL YEAR (year_precision) TO MONTH                      | INTERVAL_YEAR_TO_MONTH(y)           | INTERVAL YEAR (>= year_precision)  TO MONTH                  |
| INTERVAL DAY(day_precision) TO SECOND(fractional_seconds_precision) | INTERVAL_DAY_TO_SECOND(d, f) | INTERVAL DAY(>= day_precision)  TO SECOND(>= fractional_ seconds_precision) |
| CLOB                                                         | CLOB                                | CLOB                                                         |
| NCLOB                                                        | NCLOB                               | NCLOB                                                        |
| BLOB                                                         | BLOB                                | BLOB                                                         |

:::

Note:

- For YashanDB data types not listed in the table above, DMC does not support the migration of such data.
- For constant data, the original default value remains unchanged during conversion.
- CHAR and VARCHAR type mappings to the target are strictly in byte|char units.
- If the length of RAW exceeds 7976 bytes, out-of-row chunking will be adopted to improve performance; the maximum length shall not exceed 65534 bytes.



### Column Default Value

DMC's support specifications for column default values are as follows:

- Constant default values are supported during the metadata stage. Non-constant default values will still be synchronized during the metadata stage but will not undergo any special handling. If synchronization fails, the user must intervene to correct the issue and rerun the task.
- Default values such as functions require user confirmation regarding inter-library compatibility to avoid inconsistencies after metadata synchronization.
- Metadata validation does not check default values.
- The DDL during the incremental data synchronization phase only supports the following default values:

|Tool Type |Default Value Type |Example |
| ------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BIT                                              | 'xxxx', b'xxxx', xxxx, null                                  | '1234', b'010100', 1234, null                                |
| INT8, INT16, INT32, INT64, FLOAT, DOUBLE, NUMBER | 'xxxx', xxxx, null                                           | '1234', 1234, 3.14E2, 01000, null                            |
| CHAR, VARCHAR, NCHAR, NVARCHAR, CLOB, NCLOB      | 'xxxx', empty_clob(), null                                   | 'aaaa', empty_clob(), null                                   |
| BOOLEAN                                          | 'xxxx', true, false, null                                    | 'yes', '10', true, false, null                               |
| RAW, BLOB                                        | 'xxxx', empty_blob(), null                                   | '000111', empty_blob(), null                                 |
| DATE                                             | 'xxxx-xx-xx xx:xx:xx', null                                  | '0001-01-01 00:00:00', null                                  |
| TIME                                             | 'xx:xx:xx.xxxxxx', null                                      | '00:00:00.000000', null                                      |
| TIMESTAMP                                        | 'xxxx-xx-xx xx:xx:xx.xxxxxx', null，CURRENT_TIMESTAMP, LOCALTIME, LOCALTIMESTAMP, NOW | '1-1-1 00:00:00.000000', null，CURRENT_TIMESTAMP, LOCALTIME, LOCALTIMESTAMP, NOW |
| INTERVAL YEAR TO MONTH                           | 'xxxx-xx', null                                              | '-178000000-00', null                                        |
| INTERVAL DAY TO SECOND                           | 'xx xx:xx:xx.xxxxxx', null                                   | '-100000000 00:00:00.000000', null                           |



### Metadata

DMC has a series of specification requirements regarding the metadata of YashanDB to meet the supported synchronization range, including:

|Table |Primary Key |Column |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| -     | * Cannot be CLOB, NCLOB, BLOB, TINYBLOB, MYSQL_BLOB, MEDIUMBLOB, LONGBLOB, TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT, ENUM, SET tool types | * If the mapped tool type is VARCHAR, length cannot exceed 32000;<br>* The value range for Timestamp with local time zone and timestamp with time zone is `['0001-01-01 00:00:00.000000','9999-12-31 23:59:59.9999999']` |

- DMC uses flashback queries for YashanDB synchronization snapshot data. DDL operations on objects within the synchronization range are not allowed during the metadata synchronization and full data synchronization phases, otherwise synchronization failure will occur.
- The partition structure of partition tables is not supported for synchronization. DMC will treat partition tables as normal tables for synchronization, and partition tables can be manually created or modified on the target side.

### Data

When executing data synchronization from YashanDB to YashanDB, the following restrictions apply:



- For non-primary tables containing LOB(BLOB, CLOB, NCLOB) or out-of-line types(VARCHAR2, NVARCHAR2, RAW,etc.), the following restrictions apply:

  1. INSERTs of the same data in non-row out-of-line columns are not supported for incremental synchronization, for instance, inserting two rows of id=2 data in the example below.
  2. UPDATES of existing data with the same non-row out-of-line column data are not supported for incremental synchronization, for instance, updating id=1 data in the example below.
  3. DELETES of existing data with the same non-row out-of-line column data are not supported, for instance, deleting id=1 data in the example below.

  ```sql
  -- Full synchronization phase
  CREATE TABLE yds_test(
    id NUMBER,
    col1 BLOB,
    col2 CLOB,
    col3 nclob,
    col4 VARCHAR2(20000),
    col5 nvarchar2(10000)
  );
  INSERT INTO yds_test VALUES(1, utl_raw.cast_to_raw(RPAD('yds', 20000, 'test')), RPAD('yds', 20000, 'test'), RPAD('yds', 20000, 'test'), RPAD('yds', 20000, 'test'), RPAD('yds', 20000, 'test'));
  INSERT INTO yds_test VALUES(1, utl_raw.cast_to_raw(RPAD('yds2', 20000, 'test2')), RPAD('yds2', 20000, 'test2'), RPAD('yds2', 20000, 'test2'), RPAD('yds2', 20000, 'test2'), RPAD('yds2', 20000, 'test2'));
  
  -- Incremental phase does not support synchronizing the following insert operations
  INSERT INTO yds_test VALUES(2, utl_raw.cast_to_raw(RPAD('yds', 20000, 'test')), RPAD('yds', 20000, 'test'), RPAD('yds', 20000, 'test'), RPAD('yds', 20000, 'test'), RPAD('yds', 20000, 'test'));
  INSERT INTO yds_test VALUES(2, utl_raw.cast_to_raw(RPAD('yds2', 20000, 'test2')), RPAD('yds2', 20000, 'test2'), RPAD('yds2', 20000, 'test2'), RPAD('yds2', 20000, 'test2'), RPAD('yds2', 20000, 'test2'));
  
  -- Incremental phase does not support synchronizing the following update operations
  UPDATE yds_test SET col1 = utl_raw.cast_to_raw(RPAD('yds3', 20000, 'test3')) WHERE id = 1;
  
  -- Incremental phase does not support synchronizing the following delete operation
  delete from yds_test where id = 1;
  ```



It is recommended to check if such data exists in YashanDB to avoid data inconsistency issues.

### Others

Other specifications include:

- Schema names, table names, and column names cannot contain double quotes.
