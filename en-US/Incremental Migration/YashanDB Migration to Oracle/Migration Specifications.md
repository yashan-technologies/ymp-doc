### Database Version

#### YashanDB



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



#### Oracle

19C

### Character Set

YashanDB supports:

CHARACTER_SET: UTF-8, GBK, GB18030

NATIONAL_CHARACTER_SET: UTF16

Oracle supports:

NLS_CHARACTERSET：AL32UTF8、ZHS16GBK

NLS_NCHAR_CHARACTERSET：AL16UTF16

Migration tasks do not convert or synchronize character sets. Users must ensure that the character set range on the target end is compatible with the character set range on the source end.

### Data Types

To be compatible with various database models, DMC has customized a set of tool types for intermediate mapping between the data types on the source and target ends, and provides user-defined mapping functionality for conversion between tool types and target end data types.

The following table lists the source table types supported by DMC for migration, and the default mapping from the source end to the tool and then to the target end data types:

|YashanDB Data Type |Tool Type |Oracle Data Type |Oracle Compatible Type |
| ------------------------------------------------------------ | ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BIT(size)                                                    | BIT(n)                       | NUMBER(P), P=((size - 1) - (size - 1) / 10) / 3 + 1          | NUMBER(P, S), P - S >= ((size - 1) - (size - 1) / 10) / 3 + 1 |
| TINYINT (USE_NATIVE_TYPE=TRUE)                               | INT8                         | NUMBER(3, 0)                                                 | NUMBER(P, S), P - S >= 3                                     |
| TINYINT (USE_NATIVE_TYPE=FALSE)                              | NUMBER(38, 0)                | NUMBER(38, 0)                                                | NUMBER(P, S), P - S >= 38                                    |
| SMALLINT (USE_NATIVE_TYPE=TRUE)                              | INT16                        | NUMBER(5, 0)                                                 | NUMBER(P, S), P - S >= 5                                     |
| SMALLINT (USE_NATIVE_TYPE=FALSE)                             | NUMBER(38, 0)                | NUMBER(38, 0)                                                | NUMBER(P, S), P - S >= 38                                    |
| INTEGER (USE_NATIVE_TYPE=TRUE)                               | INT32                        | NUMBER(10, 0)                                                | NUMBER(P, S), P - S >= 10                                    |
| INTEGER (USE_NATIVE_TYPE=FALSE)                              | NUMBER(38, 0)                | NUMBER(38, 0)                                                | NUMBER(P, S), P - S >= 38                                    |
| BIGINT (USE_NATIVE_TYPE=TRUE)                                | INT64                        | NUMBER(19, 0)                                                | NUMBER(P, S), P - S >= 19                                    |
| BIGINT (USE_NATIVE_TYPE=FALSE)                               | NUMBER(38, 0)                | NUMBER(38, 0)                                                | NUMBER(P, S), P - S >= 38                                    |
| FLOAT(p) / REAL(p) (USE_NATIVE_TYPE=TRUE)                    | FLOAT                        | BINARY_FLOAT                                                 | BINARY_FLOAT, BINARY_DOUBLE                                  |
| FLOAT(p) / REAL(p) (USE_NATIVE_TYPE=FALSE)                   | ORACLE_FLOAT(p)              | FLOAT(p)                                                     | FLOAT(>= p)                                                  |
| BINARY_FLOAT                                                 | BINARY_FLOAT                 | BINARY_FLOAT                                                 | BINARY_FLOAT, BINARY_DOUBLE                                  |
| DOUBLE/BINARY_DOUBLE                                         | DOUBLE                       | BINARY_DOUBLE                                                | BINARY_DOUBLE                                                |
| NUMBER(P, S)                                                 | NUMBER(p,s)                  | NUMBER(P, S)                                                 | NUMBER(P1, S1), P1 >= P, S1 >= S                             |
| NUMBER(*)/NUMBER                                             | NUMBER                       | NUMBER                                                       | NUMBER                                                       |
| CHAR(size [byte&#124;char])                                      | CHAR(n [byte&#124;char])     | CHAR(size [byte&#124;char])                                      | CHAR(size [byte&#124;char])                                      |
| VARCHAR(size [byte&#124;char])                               | VARCHAR(n [byte&#124;char])  | VARCHAR2(size [byte&#124;char])                                  | VARCHAR2(>=size [byte&#124;char])                                |
| NCHAR(size)                                                  | NCHAR(n)                     | NCHAR(size)                                                  | NCHAR(size)                                                  |
| NVARCHAR(size)                                               | NVARCHAR(n)                  | NVARCHAR2(size)                                              | NVARCHAR2(size)                                              |
| RAW(size)/UROWID(size)                                       | RAW(n)                       | RAW(size)                                                    | RAW(>= size)                                                 |
| BOOLEAN                                                      | BOOLEAN                      | NUMBER(1)                                                    | NUMBER(P, S), P - S >= 1CHAR(p), VARCHAR(p), NCHAR(p), NVARCHAR(p), p>=1 |
| DATE                                                         | DATE                         | DATE                                                         | DATE, TIMESTAMP                                              |
| TIME                                                         | TIME                         | CHAR(15 byte)                                                | CHAR(15 byte)                                                |
| TIMESTAMP(precision)                                         | TIMESTAMP(6)                 | TIMESTAMP(6)                                                 | TIMESTAMP(>= 6)                                              |
| INTERVAL YEAR (year_precision) TO MONTH                      | INTERVAL_YEAR_TO_MONTH(y)    | INTERVAL YEAR (year_precision) TO MONTH                      | INTERVAL YEAR (year_precision) TO MONTH                      |
| INTERVAL DAY(day_precision) TO SECOND(fractional_seconds_precision) | INTERVAL_DAY_TO_SECOND(d, f) | NTERVAL DAY(day_precision) TO SECOND(fractional_seconds_precision) | INTERVAL DAY(>= day_precision) TO SECOND(>= fractional_seconds_precision) |
| CLOB                                                         | CLOB                         | CLOB                                                         | CLOB                                                         |
| NCLOB                                                        | NCLOB                        | NCLOB                                                        | NCLOB                                                        |
| BLOB                                                         | BLOB                         | BLOB                                                         | BLOB                                                         |

Notes:

- For YashanDB data types not listed in the above table, DMC does not support the migration of such data.
- For constant data, except for the following cases, all others retain their original default values for conversion:

  - BIT binary representation string (e.g., b '0101') is converted to the corresponding decimal, while others retain their original default values.
  - FLOAT(p) / REAL(p) (when USE_NATIVE_TYPE=TRUE) default values expressed in pure numbers use `TO_BINARY_FLOAT()` conversion, e.g., 3.14E8 is converted to `TO_BINARY_FLOAT('3.14E8')`. Others retain their original default values.
  - BINARY_FLOAT default values expressed in pure numbers use `TO_BINARY_FLOAT()` conversion, e.g., 3.14E8 is converted to `TO_BINARY_FLOAT('3.14E8')`. Others retain their original default values.
  - DOUBLE/BINARY_DOUBLE default values expressed in pure numbers use `TO_BINARY_DOUBLE()` conversion, e.g., 3.14E8 is converted to `TO_BINARY_DOUBLE('3.14E8')`. Others retain their original default values.
  - BOOLEAN values of "true", "TRUE", "t", "T", "yes", "YES", "y", "Y", "on", "ON", "1", true, and non-zero numbers are converted to 1; values of "false", "FALSE", "f", "F", "no", "NO", "n", "N", "off", "OFF", "0", false, and 0 are converted to 0; null remains null.
  - DATE values expressed in pure strings use `TO_DATE()` conversion, e.g., '2024-08-01' is converted to `TO_DATE('2024-08-01', 'yyyy-mm-dd hh24:mi:ss')`. Others retain their original default values.
  - TIMESTAMP(precision) values expressed in pure strings use `TO_TIMESTAMP()` conversion, e.g., '2024-08-01 18:14:12.888889' is converted to `TO_TIMESTAMP('2024-08-01 18:14:12.888889', 'yyyy-mm-dd hh24:mi:ss.ff')`. Others retain their original default values.
- When mapping CHAR and VARCHAR types to the target end, the byte|char unit must correspond strictly.
- DMC uses flashback queries to synchronize snapshot data from YashanDB. DDL statements are not allowed to be executed on objects within the sync range during the metadata sync and full data sync phases, otherwise it will result in sync failure.
- Due to the relevant limitations of the RAW type in Oracle, the length of RAW shall not exceed 32767 bytes.



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

DMC has a series of specification requirements for the metadata of YashanDB to meet the supported synchronization range, including:

- DMC uses flashback queries to synchronize snapshot data from YashanDB. DDL statements are not allowed to be executed on objects within the sync range during the metadata sync and full data sync phases, otherwise it will result in sync failure.
- The partition structure of partition tables is not supported for syncing. DMC treats partition tables as ordinary tables for syncing, and users can manually create partition tables or add partition structures to the tables on the target end.

### Data

When synchronizing data from YashanDB to Oracle database, the following limitations exist:



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



It is recommended to check if such data exists in YashanDB to avoid data inconsistency.

- Operations such as `UPDATE FROM TEST1 SET ID = ID + 1` during the incremental phase under the following table model are not supported, as this may cause primary key constraint conflicts on the target connector.

  ```sql
  CREATE TABLE TEST1(
    ID INT PRIMARY key
  );
  
  INSERT INTO TEST1 VALUES(1);
  INSERT INTO TEST1 VALUES(2);
  ```

  The reason is that the order of data received from YStream is `UPDATE FROM TEST1 SET ID = 2 WHERE ID = 1`, `UPDATE FROM TEST1 SET ID = 3 WHERE ID = 2`, which may cause primary key constraint conflicts when executing the first SQL statement on the target connector during replay.

- When adding columns with default values set to sequence, the order of filling in this column for existing data in the table is not guaranteed.

### Others

None.
