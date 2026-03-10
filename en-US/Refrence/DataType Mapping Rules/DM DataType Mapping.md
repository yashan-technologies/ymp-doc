### DM → YashanDB Data Type Mapping Rules

|Order |DM Data Type |YashanDB Type |Parameter Impact |Risks |
|----|--------------------------------|---------------------------------------|----------------------------------------------|-------------------------------------------------|
| 1    | BIT                            | BIT/BOOLEAN                           |                                              | Limited by the tool, when the target YashanDB version is less than 23.2.4, converted to BOOLEAN, user needs to evaluate impact |
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
| 16 | CHAR[(M) char]                 | CHAR[(M) char]                        | If M exceeds 8000, then truncated                       | M's length exceeds YashanDB maximum length                              |
| 17 | CHARACTER[(M) char]            | CHAR[(M) char]                        | If M exceeds 8000, then truncated                       | M's length exceeds YashanDB maximum length                              |
| 18 | VARCHAR[(M) char]              | VARCHAR[(M) char] /VARCHAR[(4*M)]     | Whether to multiply by 4 is affected by the source database parameter LENGTH_IN_CHAR, when it is 1, multiply by 4, if M exceeds 32000, then truncated | M's length exceeds YashanDB maximum length                              |
| 19 | VARCHAR2 [(M) char]            | VARCHAR[(M) char] /VARCHAR[(4*M)]     | Whether to multiply by 4 is affected by the source database parameter LENGTH_IN_CHAR, when it is 1, multiply by 4, if M exceeds 32000, then truncated | M's length exceeds YashanDB maximum length                              |
| 20 | NVARCHAR [(M) char]            | NVARCHAR[(M) ]                        | If M exceeds 16000, then truncated                      | M's length exceeds YashanDB maximum length                              |
| 21 | NVARCHAR2 [(M) char]           | NVARCHAR2[(M) ]                       | If M exceeds 16000, then truncated                      | M's length exceeds YashanDB maximum length                              |
| 22 | NCHAR [(M)]                    | NCHAR[(M) ]                           | If M exceeds 4000, then truncated                       | M's length exceeds YashanDB maximum length                              |
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
| 33 | INTERVAL DAY TO SECOND         | INTERVAL DAY TO SECOND                |                                              | Range exceeds YashanDB range                                  |
| 34 | INTERVAL YEAR TO MONTH         | INTERVAL YEAR TO MONTH                |                                              | Range exceeds YashanDB range                                  |
| 35 | DATE                           | DATE                                  |                                              | Range exceeds YashanDB range                                  |
| 36 | TIME                           | TIME                                  |                                              |                                                 |
| 37 | DATETIME[(M)]                  | TIMESTAMP[(M)]                        |                                              |                                                 |
| 38 | TIMESTAMP[(M)]                 | TIMESTAMP[(M)]                        |                                              |                                                 |
| 39 | TIME WITH TIME ZONE            | TIME                                  |                                              |                                                 |
| 40 | TIMESTAMP WITH LOCAL TIME ZONE | For versions below 23.4: TIMESTAMP                      |                                              |                                                 |
|      |                                | For versions above 23.4: TIMESTAMP WITH LOCAL TIME ZONE |                                              |                                                 |
| 41 | TIMESTAMP WITH TIME ZONE       | For versions below 23.4: TIMESTAMP                      |                                              |                                                 |
|      |                                | For versions above 23.4: TIMESTAMP WITH TIME ZONE       |                                              |                                                 |
| 42 | DATETIME WITH TIME ZONE        | TIMESTAMP                             |                                              |                                                 |

### Limitations

1. Unsupported data types: ROWID, BFILE, INTERVAL YEAR, INTERVAL MONTH, INTERVAL DAY, INTERVAL MINUTE, INTERVAL SECOND, INTERVAL DAY TO HOUR, INTERVAL DAY TO MINUTE, INTERVAL HOUR TO MINUTE, INTERVAL HOUR TO SECOND, INTERVAL HOUR, INTERVAL MINUTE TO SECOND.
2. The migration values of DATE, DATETIME, and TIMESTAMP types are based on the default time zone of the DM server.