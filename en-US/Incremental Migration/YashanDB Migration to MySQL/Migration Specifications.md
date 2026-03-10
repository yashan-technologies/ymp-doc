### Database Version

#### YashanDB

v23.4.7, must be in MySQL mode. Only supports HEAP tables, excluding system tables and temporary tables.

Supports synchronous data for a maximum of 10,000 Schemas or 1,000,000 tables.

#### MySQL

v5.7.44、v8.0.35, InnoDB engine. Only supports InnoDB tables.

### Character Set

YashanDB character set supports UTF8MB4 and its subsets, GBK, GB18030, latin1. For the additional characters in UTF8MB4 compared to UTF8MB3 (e.g., emoji), the following situations are not supported:

1. Additional characters appear in Database name, table name, column name, and default value.
2. Additional characters appear in elements of ENUM and SET.
3. Additional characters appear in emoji.

MySQL can only be specified as a UTF8MB4 character set.

Migration tasks do not perform character set conversion or synchronization, and users must ensure that the character set range of the target end is compatible with that of the source end.

When YashanDB uses GBK or GB18030 character sets, the following restrictions apply:

- Due to MySQL database limitations, the character coverage is limited. Database names and table names only support 3-byte UTF8 characters and cannot contain 4-byte UTF8 characters.
- Only approximately 21,000 Chinese characters are supported, failing to cover all Unicode Chinese characters.
- Symbol support is incomplete, missing the Euro sign (€) and special mathematical symbols.
- Lack of support for rare characters; many rare Chinese characters and ancient Chinese characters cannot be represented.
- During synchronization, TEXT, MEDIUMTEXT, TINYBLOB, BLOB, MEDIUMBLOB, LONGBLOB, MYSQL_BLOB may experience character garbles, data inconsistencies, and synchronization failures.

### Data Types

To be compatible with various database models, DMC has customized a set of tool types to perform intermediate mapping between the source and target end data types, also providing user-defined mapping functionality for conversion from tool types to target end data types.

The following table lists the source table types supported by DMC for migration, as well as the default mapping relationship from the source to the tool and then to the target data types:

|YashanDB (MySQL mode) Data Type |Tool Type |MySQL Data Type |MySQL Compatible Type |
| ----------------------------------------- | -------- | ----------------------------------------- | ------------------------------------------------------------ |
| TINYINT                                   | INT8                        | TINYINT(4)                                | TINYINT(4), SMALLINT, INTEGER, BIGINT,  NUMERIC(P, S), P - S >= 3 |
| TINYINT UNSIGNED                          | INT8_UNSIGNED               | TINYINT(3)  UNSIGNED                      | TINYINT(3) UNSIGNED, SMALLINT, SMALLINT(5) UNSIGNED, MEDIUMINT, MEDIUMINT(8) UNSIGNED,  INTEGER, INT(10) UNSIGNED, BIGINT,  BIGINT UNSIGNED,  NUMERIC(P, S), P - S >= 3, DECIMAL(P,S) UNSIGNED, P - S >= 3 |
| SMALLINT                                  | INT16                       | SMALLINT(6)                               | SMALLINT(6),  INTEGER, BIGINT NUMERIC(P, S), P - S >= 5      |
| SMALLINT UNSIGNED                         | INT16_UNSIGNED              | SMALLINT(5) UNSIGNED                      | SMALLINT(5) UNSIGNED, MEDIUMINT, MEDIUMINT(8) UNSIGNED,  INTEGER, INT(10) UNSIGNED, BIGINT,  BIGINT UNSIGNED,  NUMERIC(P, S), P - S >= 5, DECIMAL(P,S) UNSIGNED, P - S >= 5 |
| MEDIUMINT                                 | INT24                       | MEDIUMINT(8)                              | MEDIUMINT(8),  INTEGER, BIGINT,  NUMERIC(P, S), P - S >= 7   |
| MEDIUMINT UNSIGNED                        | INT24_UNSIGNED              | MEDIUMINT(8) UNSIGNED                     | MEDIUMINT(8) UNSIGNED,  INTEGER, INT(10) UNSIGNED, BIGINT,  BIGINT UNSIGNED,  NUMERIC(P, S), P - S >= 8, DECIMAL(P,S) UNSIGNED, P - S >= 8 |
| INT                                       | INT32                       | INT(11)                                   | INT(11), BIGINT,  NUMERIC(P, S), P - S >= 10                 |
| INT UNSIGNED                              | INT32_UNSIGNED              | INT(10) UNSIGNED                          | INT(10) UNSIGNED, BIGINT,  BIGINT UNSIGNED,  NUMERIC(P, S), P - S >= 10, DECIMAL(P,S) UNSIGNED, P - S >= 10 |
| BIGINT                                    | INT64                       | BIGINT(20)                                | BIGINT(20),  NUMERIC(P, S), P - S >= 19                      |
| BIGINT UNSIGNED                           | INT64_UNSIGNED              | BIGINT(20) UNSIGNED                       | BIGINT(20) UNSIGNED,  NUMERIC(P, S), P - S >= 20, DECIMAL(P,S) UNSIGNED, P - S >= 20 |
| FLOAT                                     | MYSQL_FLOAT(m, d)           | FLOAT                                     | FLOAT, DOUBLE                                                |
| FLOAT UNSIGNED                            | MYSQL_FLOAT_UNSIGNED(m, d)  | FLOAT UNSIGNED                            | FLOAT, FLOAT UNSIGNED, DOUBLE, DOUBLE UNSIGNED               |
| DOUBLE/DOUBLE PRECISION                   | MYSQL_DOUBLE(m, d)          | DOUBLE                                    | DOUBLE                                                       |
| DOUBLE UNSIGNED/DOUBLE PRECISION UNSIGNED | MYSQL_DOUBLE_UNSIGNED(m, d) | DOUBLE UNSIGNED/DOUBLE PRECISION UNSIGNED | DOUBLE, DOUBLE UNSIGNED                                      |
| DECIMAL(P)                                | NUMBER(p, s)         | DECIMAL(P,0)                              | DECIMAL(P,0), NUMERIC(P,0), varchar(size ≥ P+1)              |
| DECIMAL(P,S)                              | NUMBER(p, s)       | DECIMAL(P,S)                              | DECIMAL(P,S), NUMERIC(P,S), varchar(size ≥ P+1)              |
| DECIMAL(P) UNSIGNED                       | NUMBER_UNSIGNED(p, s)         | DECIMAL(P,0) UNSIGNED                     | DECIMAL(P,0) UNSIGNED,  NUMERIC(P,0) UNSIGNED, DECIMAL(P,0), NUMERIC(P,0), varchar(size ≥ P+1) |
| DECIMAL(P,S) UNSIGNED                     | NUMBER_UNSIGNED(p, s)         | DECIMAL(P,S) UNSIGNED                     | DECIMAL(P,S) UNSIGNED,  NUMERIC(P,S) UNSIGNED, DECIMAL(P,0), NUMERIC(P,0), varchar(size ≥ P+1) |
| CHAR(size)                                | CHAR(n char)                              | CHAR(size)                                | CHAR(size char), VARCHAR(>=size char)                        |
| VARCHAR(size)                             | VARCHAR(n char)                           | VARCHAR(size)                             | VARCHAR(>=size char)                                         |
| DATE                                      | MYSQL_DATE                                | DATE                                      | DATE, TIMESTAMP, DATETIME                                    |
| TIME(precision)                           | MYSQL_TIME                                | TIME(precision)                           | TIME, VARCHAR(>= 17 byte)                                    |
| DATETIME(precision)                       | DATETIME(p)                               | DATETIME(precision)                       | DATETIME                                                     |
| TIMESTAMP(precision)                      | TIMESTAMP_WITH_LOCAL_TIME_ZONE(precision) | TIMESTAMP(precision)                      | TIMESTAMP                                                    |
| TINYBLOB                                  | TINYBLOB                                  | TINYBLOB                                  | TINYBLOB, BLOB, MEDIUMBLOB, LONGBLOB                         |
| BLOB                                      | MYSQL_BLOB                                | BLOB                                      | BLOB, MEDIUMBLOB, LONGBLOB                                   |
| MEDIUMBLOB                                | MEDIUMBLOB                                | MEDIUMBLOB                                | MEDIUMBLOB, LONGBLOB                                         |
| LONGBLOB                                  | LONGBLOB                                  | LONGBLOB                                  | LONGBLOB                                                     |
| TINYTEXT                                  | TINYTEXT                                  | TINYTEXT                                  | TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT                         |
| TEXT                                      | TEXT                                      | TEXT                                      | TEXT, MEDIUMTEXT, LONGTEXT                                   |
| MEDIUMTEXT                                | MEDIUMTEXT                                | MEDIUMTEXT                                | MEDIUMTEXT, LONGTEXT                                         |
| LONGTEXT                                  | LONGTEXT                                  | LONGTEXT                                  | LONGTEXT                                                     |
| BINARY(size)                              | BINARY(n)                                 | BINARY(size)                              | BINARY                                                       |
| VARBINARY(size)                           | VARBINARY(n)                              | VARBINARY(size)                           | VARBINARY                                                    |

DMC does not support migrating data types in YashanDB that are not listed in the above table.

For constant data, the original default values are retained during conversion.

**NUMBER Type Mapping**

If the precision of the NUMBER type is P and the scale is S:

- When S>30 and the parameter options.dataCompatibility.decimal is overflowMapToVarchar, if P-S<=0, it maps to VARCHAR(S+3); otherwise, it maps to VARCHAR(P+2).
- When S<=0, if P-S<=65, it maps to DECIMAL(P-S, 0); otherwise, if the parameter options.dataCompatibility.decimal is overflowMapToVarchar, it maps to VARCHAR(P-S+1).
- When 0<S<=30, if P-S<=0, it maps to DECIMAL(S, S); otherwise, it maps to DECIMAL(P, S).

**Display Width**

After mapping data from the source to the target end, display width adjustments may be applied to certain types, including:

- Adding display width to the types mapped from TINYINT UNSIGNED, SMALLINT, SMALLINT UNSIGNED, INT, INT UNSIGNED, BIGINT, BIGINT UNSIGNED.
- Correcting display width for types mapped from MEDIUMINT (adjusting from 9 to 8).
- Correcting display width for types mapped from MEDIUMINT UNSIGNED (adjusting inconsistencies to 8).

**Decimal Places**

After mapping data from the source to the target end, decimal place adjustments may be applied to certain types, including:

- Correcting decimal places for types mapped from DECIMAL(P) (adding 0).

### Column Default Value

DMC's support specifications for column default values are as follows:

- Constant default values are supported during the metadata stage. Non-constant default values will still be synchronized during the metadata stage but will not undergo any special handling. If synchronization fails, the user must intervene to correct the issue and rerun the task.
- Default values such as functions require user confirmation regarding inter-library compatibility to avoid inconsistencies after metadata synchronization.
- Metadata validation does not check default values.
- The DDL during the incremental data synchronization phase only supports the following default values:

|Tool Type |Default Value Type |Example |
| ------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| INT8, INT16, INT32, INT64, FLOAT, DOUBLE, NUMBER | 'xxxx', xxxx, null                                           | '1234', 1234, 3.14E2, 01000, null                            |
| CHAR, VARCHAR, NCHAR, NVARCHAR, CLOB, NCLOB      | 'xxxx', empty_clob(), null                                   | 'aaaa', empty_clob(), null                                   |
| BOOLEAN                                          | 'xxxx', true, false, null                                    | 'yes', '10', true, false, null                               |
| RAW, BLOB                                        | 'xxxx', empty_blob(), null                                   | '000111', empty_blob(), null                                 |
| DATE                                             | 'xxxx-xx-xx xx:xx:xx', null                                  | '0001-01-01 00:00:00', null                                  |
| TIME                                             | 'xx:xx:xx.xxxxxx', null                                      | '00:00:00.000000', null                                      |
| TIMESTAMP                                        | 'xxxx-xx-xx xx:xx:xx.xxxxxx', null，CURRENT_TIMESTAMP, LOCALTIME, LOCALTIMESTAMP, NOW | '1-1-1 00:00:00.000000', null，CURRENT_TIMESTAMP, LOCALTIME, LOCALTIMESTAMP, NOW |
| INTERVAL YEAR TO MONTH                           | 'xxxx-xx', null                                              | '-178000000-00', null                                        |
| INTERVAL DAY TO SECOND                           | 'xx xx:xx:xx.xxxxxx', null                                   | '-100000000 00:00:00.000000', null                           |

- In cases where SQL_MODE has NO_ZERO_IN_DATE and NO_ZERO_DATE, the default value for TIMESTAMP type columns is not allowed to be NULL.

### Metadata

DMC has a set of criteria for YashanDB metadata to meet the supported synchronization range including:

|Table |Primary Key |Columns |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| * Whole row length must not exceed 65535 bytes<br>* Row size must not exceed 8126 bytes<br>* Number of columns must not exceed 1017<br>* Columns cannot be renamed<br>* An index can have a maximum of 16 columns<br>* Tables cannot be created under the system Schema  | * NULL default values cannot be set<br>* Length must be less than 768 when including VARCHAR/NVARCHAR types<br>* Composite primary keys cannot exceed 16 columns | * Cannot be BLOB, CLOB, NCLOB and have a default value that is not NULL<br>* Length cannot exceed 16383 bytes for VARCHAR, NVARCHAR type columns<br>* Length cannot exceed 64KB for TEXT type columns<br>* Length cannot exceed 16MB for MEDIUMTEXT type columns<br>* Length cannot exceed 536MB for LONGTEXT type columns<br>* Default value for DATETIME type columns cannot exceed 9999-12-31 23:59:59.999999<br>* Values for FLOAT, DOUBLE type columns cannot be INF, -INF, NaN<br>* For MEDIUMBLOB, LONGBLOB, MEDIUMTEXT, LONGTEXT type columns, actual data exceeding 512MB~1GB cannot exceed the MySQL parameter `max_allowed_packet / 2`<br>* LONGTEXT, LONGBLOB, MYSQL_BLOB and TEXT type columns cannot set a default value<br>* Default value expressions for columns other than time types can only be constant values |

- Due to MySQL database limitations, synchronization of sys, mysql, performance_schema, information_schema system databases is not supported.

- TIMESTAMP and DATETIME columns only support `CURRENT_TIMESTAMP`, `NOW()`, `LOCALTIME`, and `LOCALTIMESTAMP` function default values. Other functions cannot be used as column default values.

- Due to MySQL database version limitations, TIMESTAMP columns default to NOT NULL and will automatically set DEFAULT CURRENT_TIMESTAMP and ON UPDATE CURRENT_TIMESTAMP (if not explicitly specified). If NULL is allowed, it must be explicitly declared as NULL and DEFAULT CURRENT_TIMESTAMP cannot be set (unless NULL attribute is specified simultaneously).

- Due to MySQL database version limitations, in versions below 8.0, if a TIMESTAMP column specifies a precision parameter (e.g. `TIMESTAMP(6)`), CURRENT_TIMESTAMP cannot be used as the default value; the default value must match the same precision.

- DMC uses flashback query to synchronize snapshot data from YashanDB, and DDL operations on objects within the synchronization range are not allowed during metadata synchronization and full data synchronization phases, otherwise synchronization will fail.

### Data

When performing data synchronization from YashanDB to MySQL, the following restrictions apply:

- Values inserted and queried in MySQL for FLOAT or DOUBLE types may be inconsistent and cannot guarantee complete logical synchronization, leading to the following impacts:
  - FLOAT or DOUBLE values queried from the source and target sides may differ.
  - Unique constraints on FLOAT or DOUBLE types may cause constraint conflicts.
  - Synchronization of UPDATE or DELETE operations for FLOAT or DOUBLE types in tables without a primary key is not supported (WHERE conditions will concat the entire row data, making MySQL unable to match correctly), and synchronization for UPDATE or DELETE operations when FLOAT or DOUBLE types are used as primary keys is also not supported.

- In the incremental phase, LOB (BLOB, CLOB, NCLOB) types and VARCHAR types with a byte length exceeding 8000 as WHERE conditions cannot be captured. When DMC contains multiple rows of the same data (with exceptions in that column) in a table without a primary key and executes UPDATE or DELETE statements while restricting the number of rows updated and deleted (e.g., using `ROWNUM = 1` in WHERE conditions), inconsistencies in data may occur.

It is recommended to check for such data in YashanDB to avoid data inconsistency issues.

### Others

Other specifications include:

- The `lower_case_table_names` parameter value in MySQL must be either 0 or 1 (case-sensitive), other values may lead to metadata synchronization anomalies.