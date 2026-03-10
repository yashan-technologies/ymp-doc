### DM → YashanDB数据类型映射规则

|  顺序| DM数据类型| YashanDB类型| 参数影响| 存在风险|
|----|--------------------------------|---------------------------------------|----------------------------------------------|-------------------------------------------------|
| 1  | BIT                            | BIT/BOOLEAN                           |                                              | 受工具限制，当目标端YashanDB版本小于23.2.4时，转为BOOLEAN，需用户评估影响 |
| 2  | BYTE                           | TINYINT                               |                                              |                                                 |
| 3  | TINYINT                        | TINYINT                               |                                              |                                                 |
| 4  | SMALLINT                       | SMALLINT                              |                                              |                                                 |
| 5  | INT                            | INT                                   |                                              |                                                 |
| 6  | INTEGER                        | INTEGER                               |                                              |                                                 |
| 7  | BIGINT                         | BIGINT                                |                                              |                                                 |
| 8  | DEC                            | NUMBER                                |                                              |                                                 |
| 9  | DECIMAL                        | NUMBER                                |                                              |                                                 |
| 10 | REAL                           | DOUBLE                                |                                              |                                                 |
| 11 | FLOAT                          | FLOAT                                 |                                              |                                                 |
| 12 | DOUBLE                         | DOUBLE                                |                                              |                                                 |
| 13 | DOUBLE PRECISION               | DOUBLE                                |                                              |                                                 |
| 14 | NUMBER                         | NUMBER                                |                                              |                                                 |
| 15 | NUMERIC                        | NUMBER                                |                                              |                                                 |
| 16 | CHAR[(M) char]                 | CHAR[(M) char]                        | M超过8000，则截断                                  | M的长度大于YashanDB最大长度                              |
| 17 | CHARACTER[(M) char]            | CHAR[(M) char]                        | M超过8000，则截断                                  | M的长度大于YashanDB最大长度                              |
| 18 | VARCHAR[(M) char]              | VARCHAR[(M) char] /VARCHAR[(4*M)]     | 是否乘4受源库参数LENGTH_IN_CHAR影响，为1时乘4，M超过32000，则截断 | M的长度大于YashanDB最大长度                              |
| 19 | VARCHAR2 [(M) char]            | VARCHAR[(M) char] /VARCHAR[(4*M)]     | 是否乘4受源库参数LENGTH_IN_CHAR影响，为1时乘4，M超过32000，则截断 | M的长度大于YashanDB最大长度                              |
| 20 | NVARCHAR [(M) char]            | NVARCHAR[(M) ]                        | M超过16000，则截断                                 | M的长度大于YashanDB最大长度                              |
| 21 | NVARCHAR2 [(M) char]           | NVARCHAR2[(M) ]                       | M超过16000，则截断                                 | M的长度大于YashanDB最大长度                              |
| 22 | NCHAR [(M)]                    | NCHAR[(M) ]                           | M超过4000，则截断                                  | M的长度大于YashanDB最大长度                              |
| 23 | BINARY                         | BLOB                                  |                                              |                                                 |
| 24 | BLOB                           | BLOB                                  |                                              |                                                 |
| 25 | CLOB                           | CLOB                                  |                                              |                                                 |
| 26 | IMAGE                          | BLOB                                  |                                              |                                                 |
| 27 | LONG                           | CLOB                                  |                                              |                                                 |
| 28 | VARBINARY                      | BLOB                                  |                                              |                                                 |
| 29 | LONGVARBINARY                  | BLOB                                  |                                              |                                                 |
| 30 | LONGVARCHAR                    | CLOB                                  |                                              |                                                 |
| 31 | RAW                            | BLOB                                  |                                              |                                                 |
| 32 | TEXT                           | CLOB                                  |                                              |                                                 |
| 33 | INTERVAL DAY TO SECOND         | INTERVAL DAY TO SECOND                |                                              | 范围超出YashanDB范围                                  |
| 34 | INTERVAL YEAR TO MONTH         | INTERVAL YEAR TO MONTH                |                                              | 范围超出YashanDB范围                                  |
| 35 | DATE                           | DATE                                  |                                              | 范围超出YashanDB范围                                  |
| 36 | TIME                           | TIME                                  |                                              |                                                 |
| 37 | DATETIME[(M)]                  | TIMESTAMP[(M)]                        |                                              |                                                 |
| 38 | TIMESTAMP[(M)]                 | TIMESTAMP[(M)]                        |                                              |                                                 |
| 39 | TIME WITH TIME ZONE            | TIME                                  |                                              |                                                 |
| 40 | TIMESTAMP WITH LOCAL TIME ZONE | 23.4以下：TIMESTAMP                      |                                              |                                                 |
|    |                                | 23.4以上：TIMESTAMP WITH LOCAL TIME ZONE |                                              |                                                 |
| 41 | TIMESTAMP WITH TIME ZONE       | 23.4以下：TIMESTAMP                      |                                              |                                                 |
|    |                                | 23.4以上：TIMESTAMP WITH TIME ZONE       |                                              |                                                 |
| 42 | DATETIME WITH TIME ZONE        | TIMESTAMP                             |                                              |                                                 |

### 限制说明

1. 不支持数据类型：ROWID、BFILE、 INTERVAL YEAR 、INTERVAL  MONTH、 INTERVAL  DAY、INTERVAL  MINUTE 、INTERVAL  SECOND 、INTERVAL  DAY TO HOUR、INTERVAL  DAY TO MINUTE、 INTERVAL  HOUR TO MINUTE 、INTERVAL  HOUR TO SECOND、INTERVAL  HOUR、 INTERVAL  MINUTE TO SECOND。
2. DATE、DATETIME、TIMESTAMP类型数据的迁移值以DM服务端默认时区为基准。
