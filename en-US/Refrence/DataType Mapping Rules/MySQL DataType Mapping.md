### MySQL → YashanDB Data Type Mapping Rules

|Order |MySQL Data Type |YashanDB Type |Parameter Impact |Risks |
|----|---------------------|--------------------|---------------------------|---------------------------------------------|
| 1  | TINYINT             | TINYINT            |                           |                                             |
| 2  | SMALLINT            | SMALLINT           |                           |                                             |
| 3  | MEDIUMINT           | INT                |                           |                                             |
| 4  | INT                 | INT                |                           |                                             |
| 5  | BIGINT              | BIGINT             |                           |                                             |
| 6     | DECIMAL                                          | NUMBER                                         | Truncation occurs at the maximum NUMBER value if out of range | Source range may be greater than NUMBER maximum value |
| 7  | FLOAT               | FLOAT              |                           |                                             |
| 8  | DOUBLE              | DOUBLE             |                           |                                             |
| 9     | BIT [(M)]                                       | BIT [(M)]                                      |                                            | Migration only supported if Yasldr version is above 23.2.4; lower versions of Yasldr can be replaced for migration. |
| 10 | DATE                | DATE               |                           |                                             |
| 11 | DATETIME            | TIMESTAMP          |                           |                                             |
| 12 | TIMESTAMP[(M)]      | TIMESTAMP[(M)]     |                           |                                             |
| 13    | TIME                                            | 23.4 and below: VARCHAR(17)                  |                                            | Inconsistent range between target and source     |
|       |                                                     | 23.4 and above: TIME                          |                                            |                                             |
| 14 | YEAR                | SMALLINT           |                           |                                             |
| 15 | CHAR(M)             | CHAR(M CHAR)       |                           |                                             |
| 16    | VARCHAR(M)                                      | VARCHAR(M CHAR)                                |                                            | Length M exceeds YashanDB maximum length         |
| 17 | BINARY              | BLOB               |                           |                                             |
| 18 | VARBINARY           | BLOB               |                           |                                             |
| 19 | TINYBLOB            | BLOB               |                           |                                             |
| 20 | TINYTEXT            | CLOB               |                           |                                             |
| 21 | BLOB                | BLOB               |                           |                                             |
| 22 | TEXT                | CLOB               |                           |                                             |
| 23 | MEDIUMBLOB          | BLOB               |                           |                                             |
| 24 | MEDIUMTEXT          | CLOB               |                           |                                             |
| 25 | LONGBLOB            | BLOB               |                           |                                             |
| 26 | LONGTEXT            | CLOB               |                           |                                             |
| 27    | JSON                                             | JSON                                           |                                            | Length exceeds YashanDB maximum length           |
| 28 | ENUM                | VARCHAR(1024)      |                           |                                             |
| 29    | SET                                              | VARCHAR(32000)                                 |                                            | Length exceeds YashanDB maximum length           |
| 30 | TINYINT UNSIGNED    | SMALLINT           |                           |                                             |
| 31 | SMALLINT UNSIGNED   | INTEGER            |                           |                                             |
| 32 | MEDIUMINT  UNSIGNED | INTEGER            |                           |                                             |
| 33 | INT  UNSIGNED       | BIGINT             |                           |                                             |
| 34 | BIGINT  UNSIGNED    | NUMBER             |                           |                                             |

### Limitations

1. Unsupported Data Types: GEOMETRY, POINT, LINESTRING, POLYGON, MULTIPOINT, MULTILINESTRING, MULTIPOLYGON, GEOMETRYCOLLECTION, and other data types.
2. JSON type currently only supports migration of JSON data less than or equal to 32KB; data greater than 32KB must be manually modified to map to CLOB.
3. Migration values for DATE, DATETIME, and TIMESTAMP data types are based on the default time zone of the MySQL server.