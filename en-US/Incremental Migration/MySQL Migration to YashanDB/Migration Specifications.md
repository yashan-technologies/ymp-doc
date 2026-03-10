### Database Version

MySQL: v5.7.44, InnoDB engine, only supports InnoDB tables, excluding system and temporary tables.

YashanDB: v23.4.6, must be in MySQL mode.

### Character Set

Both MySQL and YashanDB character sets support UTF8MB3 and its subsets, GB2312, GBK, GB18030.

The following supplementary characters that appear in UTF8MB4 compared to UTF8MB3 are not supported:

1. Supplementary characters appear in Database names, Table names, Column names, Default values.
2. Supplementary characters appear in ENUM and SET elements.

The migration task does not perform conversion and synchronization of character sets. Users need to ensure that the character set range on the target side is compatible with the character set range on the source side.

The migration task does not synchronize incremental DDL at the Schema level. Users need to ensure that they do not modify the character set of the Schema being synchronized during the incremental synchronization phase to avoid data garbling.

### Data Types

To ensure compatibility with various database models, DMC has defined a set of tool types that serve as a mapping layer between data types on the source and target sides, providing users with custom mapping functionality for conversion between tool types and target data types.

The following table outlines the source table types supported by DMC for migration, along with the default mapping of data types from the source side to the tool and then to the target side:

|MySQL Type |Tool Type |YashanDB (MySQL mode) Data Type |YashanDB (MySQL mode) Compatible Type |
| ---------------------------------------------------- | --------------------------------- | -------------------------------------------------- | ------------------------------------------------------------ |
| TINYINT                                              | INT8                              | TINYINT                                            | TINYINT, SMALLINT, INTEGER, BIGINT, <br>NUMBER(P, S), P - S >= 3 |
| TINYINT UNSIGNED/TINYINT ZEROFILL                    | INT8_UNSIGNED                     | TINYINT UNSIGNED                                   | TINYINT UNSIGNED, SMALLINT, SMALLINT UNSIGNED, MEDIUMINT, MEDIUMINT UNSIGNED, INTEGER, INT UNSIGNED, BIGINT, BIGINT UNSIGNED, <br>NUMBER(P, S), P - S >= 3, FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S) <br/>UNSIGNED, P - S >= 3 |
| SMALLINT                                             | INT16                             | SMALLINT                                           | SMALLINT, INTEGER, BIGINT, <br>NUMBER(P, S), P - S >= 5      |
| SMALLINT UNSIGNED/SMALLINT ZEROFILL                  | INT16_UNSIGNED                    | SMALLINT UNSIGNED                                  | SMALLINT UNSIGNED, MEDIUMINT UNSIGNED, INTEGER, INT UNSIGNED, BIGINT, BIGINT UNSIGNED, <br>NUMBER(P, S), P - S >= 5, FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S) <br/>UNSIGNED, P - S >= 5 |
| MEDIUMINT                                            | INT24                             | MEDIUMINT                                          | MEDIUMINT, INTEGER, BIGINT, <br>NUMBER(P, S), P - S >= 7     |
| MEDIUMINT UNSIGNED/MEDIUMINT ZEROFILL                | INT24_UNSIGNED                    | MEDIUMINT UNSIGNED                                 | MEDIUMINT UNSIGNED, INTEGER, INT UNSIGNED, BIGINT, BIGINT UNSIGNED, <br/>NUMBER(P, S), P - S >= 8, FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S) <br/>UNSIGNED, P - S >= 8 |
| INT                                                  | INT32                             | INT                                                | INTEGER, BIGINT, <br>NUMBER(P, S), P - S >= 10               |
| INT UNSIGNED/INT ZEROFILL                            | INT32_UNSIGNED                    | INT UNSIGNED                                       | INT UNSIGNED, BIGINT, BIGINT UNSIGNED, <br/>NUMBER(P, S), P - S >= 10, FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S) <br/>UNSIGNED, P - S >= 10 |
| BIGINT                                               | INT64                             | BIGINT                                             | BIGINT, <br>NUMBER(P, S), P - S >= 19                        |
| BIGINT UNSIGNED/BIGINT ZEROFILL                      | INT64_UNSIGNED                    | BIGINT UNSIGNED                                    | BIGINT UNSIGNED, NUMBER(P, S), P - S >= 20, FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S) <br/>UNSIGNED, P - S >= 20 |
| FLOAT(magnitude, division)                           | MYSQL_FLOAT(m, d)                 | FLOAT                                              | FLOAT , DOUBLE/DOUBLE PRECISION                              |
| FLOAT(magnitude, division) UNSIGNED/FLOAT ZEROFILL   | MYSQL_FLOAT_UNSIGNED(m, d)        | FLOAT UNSIGNED                                     | FLOAT, FLOAT UNSIGNED, DOUBLE/DOUBLE PRECISION, DOUBLE [PRECISION] UNSIGNED |
| DOUBLE/DOUBLE(magnitude, division) PRECISION         | MYSQL_DOUBLE(m, d)                | DOUBLE/DOUBLE PRECISION                            | DOUBLE/DOUBLE PRECISION                                      |
| DOUBLE(magnitude, division) UNSIGNED/DOUBLE ZEROFILL | MYSQL_DOUBLE_UNSIGNED(m, d)       | DOUBLE [PRECISION] UNSIGNED                        | DOUBLE/DOUBLE PRECISION, DOUBLE [PRECISION] UNSIGNED         |
| FIXED/DEC/NUMERIC/DECIMAL(p,s)                       | NUMBER(p,s)                       | FIXED/DEC/NUMERIC/<br>DECIMAL(P, S)                | FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S)                         |
| FIXED/DEC/NUMERIC/DECIMAL(p,s) UNSIGNED ZEROFILL     | NUMBER_UNSIGNED(p,s)              | FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S) <br/>UNSIGNED | FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S), FIXED/DEC/NUMERIC/<br/>DECIMAL(P, S) <br/>UNSIGNED |
| CHAR(n)                                              | CHAR(n char)                      | CHAR(size)                                         | CHAR(size char)                                              |
| VARCHAR(n)                                           | VARCHAR(n char)                   | VARCHAR(size)                                      | VARCHAR(>=size char)                                         |
| DATE                                                 | MYSQL_DATE                        | DATE                                               | DATE, TIMESTAMP, DATETIME                                    |
| TIME(precision)                                      | MYSQL_TIME(p)                     | TIME(precision)                                    | TIME, VARCHAR(>= 17 byte)                                    |
| DATETIME(precision)                                  | DATETIME(p)                       | DATETIME(precision)                                | DATETIME, TIMESTAMP                                          |
| TIMESTAMP(precision)                                 | TIMESTAMP_WITH_LOCAL_TIME_ZONE(p) | TIMESTAMP(precision)                               | TIMESTAMP                                                    |
| YEAR                                                 | YEAR                              | SMALLINT                                           | SMALLINT, INTEGER, BIGINT, <br/>NUMBER(P, S), P - S >= 4     |
| TINYBLOB                                             | TINYBLOB                          | TINYBLOB                                           | BLOB                                                         |
| BLOB                                                 | MYSQL_BLOB                        | BLOB                                               | BLOB                                                         |
| MEDIUMBLOB                                           | MEDIUMBLOB                        | MEDIUMBLOB                                         | BLOB                                                         |
| LONGBLOB                                             | LONGBLOB                          | LONGBLOB                                           | BLOB                                                         |
| TINYTEXT                                             | TINYTEXT                          | TINYTEXT                                           | CLOB                                                         |
| TEXT                                                 | TEXT                              | TEXT                                               | CLOB                                                         |
| MEDIUMTEXT                                           | MEDIUMTEXT                        | MEDIUMTEXT                                         | CLOB                                                         |
| LONGTEXT                                             | LONGTEXT                          | LONGTEXT                                           | CLOB                                                         |
| ENUM                                                 | ENUM                              | BINARY(size)                                       | BINARY(size)                                                 |
| SET                                                  | SET                               | VARBINARY(size)                                    | VARBINARY(size)                                              |
| BINARY(size)                                         | BINARY(n)                         | VARCHAR(size)                                      | VARCHAR(size)                                                |
| VARBINARY(size)                                      | VARBINARY(n)                      | VARCHAR(size)                                      | VARCHAR(size)                                                |

Among them:

- For MySQL data types not listed in the table above, DMC does not support migration of such data.
- For constant data, the original default values are preserved during conversion.
- ZEROFILL is only syntactically compatible. YashanDB (mysql mode) supports ZEROFILL syntax but does not implement any automatic zero-filling functionality, storing values as original numbers.

**NUMBER Type Mapping**

For a NUMBER type with precision P and scale S:

- When S > 30 and the parameter options.dataCompatibility.decimal is set to overflowMapToVarchar, if P-S <= 0, it maps to VARCHAR(S+3); otherwise, it maps to VARCHAR(P+2).
- When S <= 0, if P-S <= 65, it maps to DECIMAL(P-S, 0); otherwise, if options.dataCompatibility.decimal is set to overflowMapToVarchar, it maps to VARCHAR(P-S+1).
- When 0 < S <= 30, if P-S <= 0, it maps to DECIMAL(S, S); otherwise, it maps to DECIMAL(P, S).

**Numeric Type Width**

The number inside the parentheses for numeric types (e.g., `INT(11)`) only represents display width and does not affect the actual storage range. Even if `ZEROFILL` is specified (e.g., `0001`), the synchronization tool may ignore these display attributes.

For example, `TINYINT(3) ZEROFILL` storing `5` will display as `005`, but may be synchronized back to a normal `5`.



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

DMC has a series of specification requirements for MySQL metadata, intended to meet the supported synchronization scope, including:

|Table |Primary Key |Column |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| * Whole row length must not exceed 65535 bytes<br>* Tables cannot be renamed across Schemas<br>* Cannot consist entirely of virtual columns, even if non-virtual columns are added during incremental stages | * Cannot be prefix primary keys<br>* Cannot be TINYBLOB, BLOB, MEDIUMBLOB, LONGBLOB, TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT, ENUM, SET types<br>* Cannot set NULL as default value<br>* Length must be less than 768 if VARCHAR/NVARCHAR types are included<br>* Composite primary keys can only be deleted when all primary key columns are deleted together, and cannot have VARCHAR types when deleted | * VARCHAR type columns length must not exceed 65535<br>* BLOB, CLOB, NCLOB type columns cannot have NULL as default value<br>* Length of VARCHAR, NVARCHAR type columns cannot exceed 21844<br>* Default values for DATETIME type columns cannot exceed 9999-12-31 23:59:59.999999<br>* Values for FLOAT, DOUBLE type columns cannot be INF, -INF, NaN |

- If part of a field's content is used as the primary key (e.g., the first 10 characters of `VARCHAR(255)`), the synchronization tool will synchronize it as the full field content as the primary key, i.e., `primary key(name(10))` will synchronize as `primary key(name)`.

- Only auto_increment attributes on primary key columns are supported for synchronization, and auto-increment values are not corrected.

- Except for blacklisted columns, the number of columns in the source and target tables must be the same, column names must match, and column types must be compatible.

- When adding new columns, specifying column position using the keywords FIRST or AFTER is supported, but this does not take effect during synchronization, meaning new columns are always appended to the end of the target table.

- Schema-level metadata changes are not supported.

- When fault tolerance is enabled, tables with all virtual columns are supported for synchronization, and all subsequent DDL (except DROP TABLE) and DML will be ignored during fault tolerance.

- When the MySQL parameter lower_case_table_names=0 is set, it is not allowed to have table names that are the same but differ in case within the same Database (e.g., Orders and orders), and it is not allowed to have columns in the same table that differ only in case (e.g., id and ID).

### Data

When performing data synchronization from MySQL to YashanDB, the following restrictions apply:

- Incremental data synchronization for XA transactions on the MySQL source side is not supported.

- The values of FLOAT or DOUBLE types inserted into MySQL may differ from queried values, hence cannot match in SQL WHERE conditions, leading to following implications:
  - The FLOAT or DOUBLE type values queried from the source and target sides may differ.
  - Unique constraints involving FLOAT or DOUBLE types may lead to constraint conflicts.
  - Synchronization of UPDATE and DELETE operations for FLOAT or DOUBLE types is not supported in tables without primary keys (WHERE conditions will concatenate full row data making it impossible for MySQL to match correctly), nor is it supported for FLOAT or DOUBLE types when used as primary keys.
  
- When a MySQL table lacks a primary key and contains LOB type fields, there may be a risk of data inconsistency during UPDATE or DELETE operations.

- Data for incremental synchronization is obtained via the MySQL Binlog Connector. The FLOAT/DOUBLE data retrieved this way may differ in precision from the actual values stored in MySQL, thus:
  - If the primary key of a MySQL table includes FLOAT or DOUBLE type fields, data synchronization inconsistencies or primary key conflicts may arise.
  - If a MySQL table has no primary key and includes FLOAT or DOUBLE type fields, data synchronization inconsistencies may arise.

- Risks of synchronizing auto-increment primary key columns:
  - If a new primary key column is set to auto-increment (`AUTO_INCREMENT`), the synchronization tool only copies the DDL statement (e.g., adding the column) but does not ensure historical data's auto increment values are consistent.
  - Unique keys with auto-increment (`AUTO_INCREMENT`) are not synchronized, nor is the unique key itself. Existing data defaults to null, leading to inconsistencies in historical data.
  - The tool does not synchronize constraints. When new columns are added, if the source side is non-nullable, MySQL will automatically fill in non-null default values, whereas the target side will fill as null, causing data inconsistencies.
  
- During data synchronization, DMC will set the YashanDB parameter NO_ZERO_IN_DATE to TRUE. If there are zero values for months or days in the source DATE type, synchronization will fail.

### Others

Other specifications include:

- The value of the lower_case_table_names parameter in MySQL must be either 0 or 1; other values may cause synchronization anomalies in metadata.
- It is required that MySQL does not use `/*!` syntax; DMC will ignore such statements.
- Object names are not allowed to contain backticks.
