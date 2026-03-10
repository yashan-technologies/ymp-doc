### DB2 to YashanDB Data Type Mapping Rules and Support Status

|No. |DB2 Source Type |YashanDB Mapped Type |Evaluation Support Status |Data Migration Support Status |Data Validation Support Status |Remarks |
|----|----------------------------|--------------|--------|----------|--|-----------------------------|
| 1  | SMALLINT                   | SMALLINT     | Native Compatible   | Supported       | |                             |
| 2  | INTEGER (alias INT)             | INTEGER      | Native Compatible   | Supported       | |                             |
| 3  | BIGINT                     | BIGINT       | Native Compatible   | Supported       | |                             |
| 4  | NUMERIC                    | NUMBER       | Native Compatible   | Supported       |  |                             |
| 5  | DOUBLE PRECISION (alias DOUBLE) | DOUBLE       | Automatic Compatible   | Supported       | |                             |
| 6  | REAL                       | REAL         | Native Compatible   | Not Supported      |  |                             |
| 7  | DECIMAL                    | NUMBER       | Automatic Compatible   | Supported       |  |                             |
| 8  | FLOAT                      | FLOAT        | Native Compatible   | Not Supported          |  |                             |
| 9  | BOOLEAN                    | BOOLEAN      | Native Compatible   | Not Supported          |  |                             |
| 10 | CHAR                       | CHAR         | Native Compatible   | Supported       | |                             |
| 11 | VARCHAR                    | VARCHAR      | Native Compatible   | Supported       | | VARCHAR: Length exceeds 32000, restricted to 32000 |
| 12 | CLOB                       | CLOB         | Native Compatible   | Supported       | |                             |
| 13 | BINARY                     | BINARY       | Native Compatible   | Not Supported          |  |                             |
| 14 | VARBINARY                  | VARBINARY    | Native Compatible   | Not Supported          |  |                             |
| 15 | BLOB                       | BLOB         | Native Compatible   | Supported       | |                             |
| 16 | TIME                       | TIME         | Native Compatible   | Not Supported          |  |                             |
| 17 | TIMESTAMP                  | TIMESTAMP    | Native Compatible   | Supported       | |                             |
| 18 | DATE                       | DATE         | Native Compatible   | Not Supported          |  |                             |
| 19 | LONG VARCHAR               | CLOB         | Automatic Compatible   | Supported         |  |                             |
| 20 | LONG VARGRAPHIC            | CLOB         | Automatic Compatible   | Supported         |  |                             |

### Limitation Notes

1. Other unsupported types: REFERENCE, DECFLOAT, CURSOR, and other unmentioned types.