



### Database Version

#### Oracle

12, 19c, only supports HEAP tables and standalone primary-standby deployment, and does not include system tables or temporary tables.

Schema support range:

|Oracle Schema Type |Data Location |User Connection Location |METADATA Mode |SNAPSHOT Mode |INCREMENT Mode |INITIAL Mode |
| -------------- | -------- | ------------ | ------------ | ------------ | ------------- | ----------- |
| Traditional Schema | Local Database | CDB$ROOT                | ✅             | ✅             | ✅               | ✅           |
| Multitenant Schema  | Specific PDB  | PDB                      | ✅             | ✅             | ❌               | ❌           |
| Multitenant Schema  | Specific PDB  | CDB$ROOT                | ✅             | ✅             | ✅               | ✅           |
| Multitenant Schema  | Multiple PDBs | -                        | ❌             | ❌             | ❌               | ❌           |
| Multitenant Schema  | CDB$ROOT      | CDB$ROOT                | ✅             | ✅             | ✅               | ✅           |

It does not support synchronizing data from multiple PDBs simultaneously under one DMC process and does not support application container type PDBs.

#### YashanDB

Version: 23.2.7 and above.

Deployment Form: Standalone, YAC

Table Type: HEAP

> **Note**:
>
> The `timestamp` type in YashanDB **v23.4.6** has a maximum precision limit. If the precision of the `timestamp` type in the Oracle database to be synchronized exceeds this limit, a **metadata verification failure** will be triggered during migration to YashanDB.

### Character Set

Oracle Character Set Support:

NLS_CHARACTERSET：AL32UTF8

NLS_NCHAR_CHARACTERSET：AL16UTF16

YashanDB Character Set Support:

CHARACTER_SET：UTF-8、GBK、GB18030

NATIONAL_CHARACTER_SET：UTF16

Migration tasks do not convert or synchronize character sets; users must ensure the target character set range is compatible with the source character set range.

### Data Types

To be compatible with various database models, DMC has defined a set of custom tool types to facilitate transitional mapping between source and target data types while providing a user-defined mapping functionality for converting tool types to target data types.

The following table shows the source table types supported by DMC and the default mapping relationship from source to tool to target data types:

|Oracle Data Type |Tool Type |YashanDB Data Type |YashanDB Compatible Type |
| ------------------------------------------------------------ | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BINARY_FLOAT                                             | BINARY_FLOAT     | FLOAT/BINARY_FLOAT when USE_NATIVE_TYPE=TRUE<br>BINARY_FLOAT when USE_NATIVE_TYPE=FALSE | BINARY_FLOAT, BINARY_DOUBLE                                |
| DOUBLE/BINARY_DOUBLE                                     | BINARY_DOUBLE    | DOUBLE/BINARY_DOUBLE when USE_NATIVE_TYPE=TRUE<br>BINARY_DOUBLE when USE_NATIVE_TYPE=FALSE | DOUBLE/BINARY_DOUBLE (alias) when USE_NATIVE_TYPE=TRUE; BINARY_DOUBLE when USE_NATIVE_TYPE=FALSE |
| NUMBER(P, S)                                                 | NUMBER(P, S)    | NUMBER(P, S)                                                 | NUMBER(P1, S1), P1 >= P, S1 >= S                            |
| FLOAT(*)                                               | ORACLE_FLOAT(*)  | NUMBER when USE_NATIVE_TYPE=TRUE<br>FLOAT(*) when USE_NATIVE_TYPE=FALSE | NUMBER                                                    |
| NUMBER(*)/NUMBER/DEC/NUMERIC/DECIMAL                         | NUMBER          | NUMBER                                                       | NUMBER                                                       |
| CHAR(size [byte&#124;char])                            | CHAR(n [byte&#124;char])  | CHAR(size [byte&#124;char]), strictly corresponding units | CHAR(size [byte&#124;char]), strictly corresponding units |
| VARCHAR2(size [byte&#124;char])                        | VARCHAR(n [byte&#124;char])  | VARCHAR(size [byte&#124;char]), strictly corresponding units  | VARCHAR(>=size [byte&#124;char]), strictly corresponding units |
| NCHAR(size)                                                  | NCHAR(n)                | NCHAR(size)                                                  | NCHAR(size)                                                  |
| NVARCHAR2(size)                                              | NVARCHAR(n)                | NVARCHAR(size)                                               | NVARCHAR(>=size)                                             |
| RAW(size)                                       | RAW(n)                | RAW(size)                                                    | RAW(>= size)                                                 |
| DATE                                                         | DATE                | DATE                                                         | DATE、TIMESTAMP                                              |
| TIMESTAMP(precision)                                   | TIMESTAMP(p)     | TIMESTAMP(precision)                                    | TIMESTAMP(>= 6), no precision validation if options.dataCompatibility.validateTimestampPrecision is false |
| TIMESTAMP(precision) WITH LOCAL TIME ZONE                    | TIMESTAMP_WITH_LOCAL_TIME_ZONE(p)                | TIMESTAMP(precision) WITH LOCAL TIME                         | TIMESTAMP(precision) WITH LOCAL TIME                         |
| TIMESTAMP(precision) WITH TIME ZONE                          | TIMESTAMP_WITH_TIME_ZONE(p)                | TIMESTAMP(precision) WITH TIME ZONE                          | TIMESTAMP(precision) WITH TIME ZONE                          |
| INTERVAL YEAR (year_precision) TO MONTH                      | INTERVAL_YEAR_TO_MONTH(y)                | INTERVAL YEAR (year_precision)  TO MONTH                     | INTERVAL YEAR (>= year_precision)  TO MONTH                  |
| INTERVAL DAY(day_precision) TO SECOND(fractional_ seconds_precision) | INTERVAL_DAY_TO_SECOND(d, f)                | INTERVAL DAY(day_precision)  TO SECOND(fractional_ seconds_precision) | INTERVAL DAY(>= day_precision)  TO SECOND(>= fractional_ seconds_precision) |
| CLOB                                                         | CLOB                | CLOB                                                         | CLOB                                                         |
| NCLOB                                                        | NCLOB                | NCLOB                                                        | NCLOB                                                        |
| BLOB                                                         | BLOB                | BLOB                                                         | BLOB                                                         |

Where:

- DMC does not support migration when the length of a RAW exceeds 7936.
- DMC does not support the migration of Oracle data types such as LONG, LONG RAW,UROWID, JSON, XMLTYPE, custom type, ROWID, SDO_GEOMETRY, SDO_GEORASTER, SDO_TOPO_GEOMETRY, and BFILE.

- For constant data, unless specified otherwise, the original default values are maintained:

  - The default value of BINARY_FLOAT expressed as a pure number uses `TO_BINARY_FLOAT()` for conversion, e.g., 3.14E8 is converted to `TO_BINARY_FLOAT('3.14E8')`. The rest retain the original default values.

  - The default value of DOUBLE/BINARY_DOUBLE expressed as a pure number uses `TO_BINARY_DOUBLE()` for conversion, e.g., 3.14E8 is converted to `TO_BINARY_DOUBLE('3.14E8')`. The rest retain the original default values.

  - The default value of DATE expressed as a pure string uses `TO_DATE()` for conversion, e.g., '2024-08-01' is converted to `TO_DATE('2024-08-01', 'yyyy-mm-dd hh24:mi:ss')`. The rest retain the original default values.

  - The default value of TIMESTAMP(precision) expressed as a pure string uses `TO_TIMESTAMP()` for conversion, e.g., '2024-08-01 18:14:12.888889' is converted to `TO_TIMESTAMP('2024-08-01 18:14:12.888889', 'yyyy-mm-dd hh24:mi:ss.ff')`. The rest retain the original default values.

- When mapping CHAR and VARCHAR types to the target, the byte|char units strictly correspond.

- It does not support synchronizing partition table structures; DMC treats partition tables as regular tables for synchronization, and you can manually create partition tables or add table partition structures on the target.

- The syntax of sequences in Oracle is compatible with YashanDB, for example, Oracle sequences allow creating a sequence with maxValue less than minValue, which is not allowed in YashanDB. This incompatibility will lead to migration failure.



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

DMC has a series of specification requirements for Oracle metadata to meet the supported synchronization range, including:

|Table |Primary Key |Columns |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| * Name length cannot exceed 64 bytes | * Cannot be RAW type<br>* Cannot be CLOB, NCLOB, BLOB, TINYBLOB, MYSQL_BLOB, MEDIUMBLOB, LONGBLOB, TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT, ENUM, SET tool types | * Name length cannot exceed 64 bytes<br>* Cannot be RAW, LONG, LONG RAW types when incremental synchronization is enabled<br>* If the mapped tool type is VARCHAR, the length must not exceed 32000<br>* When Timestamp type has default value TO_TIMESTAMP(), the precision of TO_TIMESTAMP() must not exceed 6<br>* The data range for Timestamp with local time zone and Timestamp with time zone types is `['0001-01-01 00:00:00.000000','9999-12-31 23:59:59.9999999']` |

- Due to LOG MINER limitations, Oracle does not support rolling back specified save points in bulk inserts of tables containing LOB or out-of-line types (BLOB, CLOB, NCLOB, VARCHAR2, NVARCHAR2, etc.).

- Incremental DDL is not supported unless filtered via source.options.connectorParameter._filterDdlPattern configuration.

- When options.syncMode is initial, incremental DDL occurring after the oldest active transaction start point is not supported.

- Synchronization of all tables under SYS schema is not supported. To synchronize tables under SYS, all table names must be fully configured.

- When the source sequence last_number > max_value, that sequence is not supported for synchronization.

### Data

When performing incremental synchronization from Oracle to YashanDB, the following restrictions apply:

- SESSION-level sequences and sequences that can be shared across multiple PDBs in CDB will not push HWM during incremental synchronization.

- UPDATE operations like id=id+1 cannot be performed on primary keys.



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



- When adding a new column with a default value of a sequence, the fill order for that column in existing data in the table is not guaranteed.

- Due to advanced operations of the DBMS_LOB package (DBMS_LOB.WRITE, DBMS_LOB.TRIM, DBMS_LOB.ERASE), which can cause data inconsistency during incremental synchronization, it is not supported for incremental data synchronization on tables containing LOB types (BLOB, CLOB, NCLOB, VARCHAR2, NVARCHAR2, etc.):

     - DBMS_LOB.WRITE operations only support writing data at the Oracle Offset of 0; otherwise, the task will report an error.
     - When the source side executes DBMS_LOB.WRITE operations, DMC has the following limitations:

       - Data truncation: only the actual written data segment is synchronized; original LOB content is not preserved.
       - Lack of merge logic: DMC does not have the capability to merge new data with existing data.

     - LOB_TRIM events triggered by UPDATE statements synchronize normally, ensuring data consistency.
     
- event will not be monitored, and data cannot be synchronized to the target end, resulting in data inconsistency. Please use it with caution.


### Others

Other specifications include:

- Schema names, table names, and column names must not contain double quotes.
- Schema names and table names must not contain special characters such as []{}().`~\; LogMiner cannot parse these.
- Object names (such as table names, column names, sequence names, etc.) must not exceed 64 characters.
- Ordinary Oracle users do not currently support **[Full Data Migration]** + **[Incremental data migration]**.

