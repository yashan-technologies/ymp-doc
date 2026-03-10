This document explains the data types supported for full verification and their corresponding mappings.

## Restriction Notes

- Full verification does not yet support verification for other data type mappings; if there are other data type mappings for verification, they will be skipped and not verified.
- Data types not supported for verification in Oracle: JSON, XMLTYPE, custom data types.
- Data types not supported for verification in DM: IMAGE, custom data types.
- Data types not supported for verification in MySQL: JSON, ENUM, SET, custom data types.
- Data types not supported for verification in PostgreSQL: cidr, circle, jsonb, line, lseg, macaddr, macaddr8, money, path, pg_lsn, pg_snapshot, point, polygon, tsquery, tsvector, txid_snapshot, uuid, BIT, BIT VARYING, BYTEA, JSON, TEXT, XML, INTERVAL, INTERVAL YEAR, INTERVAL MONTH, INTERVAL DAY, INTERVAL MINUTE, INTERVAL SECOND, INTERVAL DAY TO HOUR, INTERVAL DAY TO MINUTE, INTERVAL HOUR TO MINUTE, INTERVAL HOUR TO SECOND, INTERVAL HOUR, INTERVAL MINUTE TO SECOND, and other types.

## Data Types

### DM Data Type Mapping

|No |DM Data Type |YashanDB Type |
|----|--------------------------------|-----------------------------------|
| 1  | BIT                            | BIT/BOOLEAN                       |
| 2  | BYTE                           | TINYINT                           |
| 3  | TINYINT                        | TINYINT                           |
| 4  | SMALLINT                       | SMALLINT                          |
| 5  | INT                            | INT                               |
| 6  | INTEGER                        | INTEGER                           |
| 7  | BIGINT                         | BIGINT                            |
| 8  | DEC                            | NUMBER                            |
| 9  | DECIMAL                        | NUMBER                            |
| 10 | REAL                           | DOUBLE                            |
| 11 | FLOAT                          | FLOAT                             |
| 12 | DOUBLE                         | DOUBLE                            |
| 13 | DOUBLE PRECISION               | DOUBLE                            |
| 14 | NUMBER                         | NUMBER                            |
| 15 | NUMERIC                        | NUMBER                            |
| 16 | CHAR[(M) char]                 | CHAR[(M) char]                    |
| 17 | CHARACTER[(M) char]            | CHAR[(M) char]                    |
| 18 | VARCHAR[(M) char]              | VARCHAR[(M) char] /VARCHAR[(4*M)] |
| 19 | VARCHAR2 [(M) char]            | VARCHAR[(M) char] /VARCHAR[(4*M)] |
| 20 | NVARCHAR [(M) char]            | NVARCHAR[(M) ]                    |
| 21 | NVARCHAR2 [(M) char]           | NVARCHAR2[(M) ]                   |
| 22 | NCHAR [(M)]                    | NCHAR[(M) ]                       |
| 23 | INTERVAL DAY TO SECOND         | INTERVAL DAY TO SECOND            |
| 24 | INTERVAL YEAR TO MONTH         | INTERVAL YEAR TO MONTH            |
| 25 | DATE                           | DATE                              |
| 26 | TIME                           | TIME                              |
| 27 | DATETIME[(M)]                  | TIMESTAMP[(M)]                    |
| 28 | TIMESTAMP[(M)]                 | TIMESTAMP[(M)]                    |
| 29 | TIME WITH TIME ZONE            | TIME                              |
| 30 | TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP                         |
| 31 | TIMESTAMP WITH TIME ZONE       | TIMESTAMP                         |
| 32 | DATETIME WITH TIME ZONE        | TIMESTAMP                         |
| 33 | RAW                            | RAW                               |
| 34 | LONG                           | CLOB                              |
| 35 | BLOB                           | BLOB                              |
| 36 | CLOB                           | CLOB                              |
| 37 | LONGVARBINARY                  | BLOB                              |
| 38 | LONGVARCHAR                    | CLOB                              |
| 39 | TEXT                           | CLOB                              |

### Oracle Data Type Mapping

|No |Oracle Data Type |YashanDB Type |
|-----|------------------------------------------------------|------------------------------------|
| 1   | VARCHAR2(size [BYTE or CHAR])                        | VARCHAR (size [BYTE or CHAR])      |
| 2   | NVARCHAR2(N)                                         | NVARCHAR(N)                        |
| 3   | NUMBER[ (p[, s]) ]                                   | NUMBER                             |
| 4   | FLOAT[(p)]                                           | FLOAT[(p)]                         |
| 5   | DATE                                                 | DATE                               |
| 6   | BINARY_FLOAT                                         | BINARY_FLOAT                       |
| 7   | BINARY_DOUBLE                                        | BINARY_DOUBLE                      |
| 8   | TIMESTAMP[(M)]                                       | TIMESTAMP                          |
| 9   | TIMESTAMP [(M)] WITH TIME ZONE                       | TIMESTAMP                          |
| 10  | TIMESTAMP [(M)] WITH LOCAL TIME ZONE                 | TIMESTAMP                          |
| 11  | INTERVAL YEAR [(M)] TO MONTH                         | INTERVAL YEAR [(M)] TO MONTH       |
| 12  | INTERVAL DAY [(M)] TO SECOND [(N)]                   | INTERVAL DAY [(M)] TO SECOND [(N)] |
| 13  | CHAR [(size [BYTE or CHAR])]                         | CHAR [(size [BYTE or CHAR])]       |
| 14  | NCHAR[(size)]                                        | NCHAR[size]                        |
| 15  | INT/INTEGER/SMALLINT/DEC/NUMERIC/decimal Essentially NUMBER | NUMBER                             |
| 16  | DOUBLE PRECISION                                     | NUMBER                             |
| 17  | RAW(size)                                            | RAW(size)                          |
| 18  | LONG RAW                                             | LONG RAW                           |
| 19  | CLOB                                                 | CLOB                               |
| 20  | NCLOB                                                | NCLOB                              |
| 21  | BLOB                                                 | BLOB                               |
| 22  | LONG                                                 | CLOB                               |

### MySQL Data Type Mapping

|No |MySQL Data Type |YashanDB Type |
|----|--------------------|------------|
| 1  | INT                | INT        |
| 2  | TINYINT            | TINYINT    |
| 3  | SMALLINT           | SMALLINT   |
| 4  | MEDIUMINT          | INT        |
| 5  | BIGINT             | BIGINT     |
| 6  | TINYINT UNSIGNED   | SMALLINT   |
| 7  | SMALLINT UNSIGNED  | INTEGER    |
| 8  | MEDIUMINT UNSIGNED | INTEGER    |
| 9  | INT UNSIGNED       | BIGINT     |
| 10 | BIGINT UNSIGNED    | NUMBER     |
| 11 | VARCHAR            | VARCHAR    |
| 12 | CHAR               | CHAR       |
| 13 | FLOAT              | FLOAT      |
| 14 | DECIMAL            | NUMBER     |
| 15 | DOUBLE             | DOUBLE     |
| 16 | DATE               | DATE       |
| 17 | TIME               | VARCHAR    |
| 18 | DATETIME           | TIMESTAMP  |
| 19 | TIMESTAMP          | TIMESTAMP  |
| 20 | YEAR               | SMALLINT   |
| 21 | BINARY             | BLOB       |
| 22 | VARBINARY          | BLOB       |
| 23 | TINYBLOB           | BLOB       |
| 24 | TINYTEXT           | CLOB       |
| 25 | BLOB               | BLOB       |
| 26 | TEXT               | CLOB       |
| 27 | MEDIUMBLOB         | BLOB       |
| 28 | MEDIUMTEXT         | CLOB       |
| 29 | LONGBLOB           | BLOB       |
| 30 | LONGTEXT           | CLOB       |
| 31 | BIT                | BIT        |

### MySQL TO YASHANDB (MySQL Mode) Data Type Mapping

|No |MySQL Data Type |YashanDB Type |
|----|--------------------|------------|
| 1  | INT                | INT        |
| 2  | TINYINT            | TINYINT    |
| 3  | SMALLINT           | SMALLINT   |
| 4  | MEDIUMINT          | INT        |
| 5  | BIGINT             | BIGINT     |
| 6  | TINYINT UNSIGNED   | SMALLINT   |
| 7  | SMALLINT UNSIGNED  | INTEGER    |
| 8  | MEDIUMINT UNSIGNED | INTEGER    |
| 9  | INT UNSIGNED       | BIGINT     |
| 10 | BIGINT UNSIGNED    | NUMBER     |
| 11 | VARCHAR            | VARCHAR    |
| 12 | CHAR               | CHAR       |
| 13 | FLOAT              | FLOAT      |
| 14 | DECIMAL            | NUMBER     |
| 15 | DOUBLE             | DOUBLE     |
| 16 | DATE               | DATE       |
| 17 | TIME               | TIME       |
| 18 | DATETIME           | TIMESTAMP  |
| 19 | TIMESTAMP          | TIMESTAMP  |
| 20 | YEAR               | SMALLINT   |
| 21 | BINARY             | BLOB       |
| 22 | VARBINARY          | BLOB       |
| 23 | TINYBLOB           | BLOB       |
| 24 | TINYTEXT           | CLOB       |
| 25 | BLOB               | BLOB       |
| 26 | TEXT               | CLOB       |
| 27 | MEDIUMBLOB         | BLOB       |
| 28 | MEDIUMTEXT         | CLOB       |
| 29 | LONGBLOB           | BLOB       |
| 30 | LONGTEXT           | CLOB       |
| 31 | BIT                | BIT        |

### PostgreSQL Data Type Mapping

|No |PostgreSQL Data Type |YashanDB Type |
|----|-----------------------------|------------------------|
| 1  | SMALLINT                    | SMALLINT               |
| 2  | INT                         | INT                    |
| 3  | INTEGER                     | INTEGER                |
| 4  | BIGINT                      | BIGINT                 |
| 5  | DOUBLE PRECISION            | DOUBLE                 |
| 6  | REAL                        | REAL                   |
| 7  | BOOLEAN                     | BOOLEAN                |
| 10 | CHARACTER                   | CHAR                   |
| 11 | CHARACTER VARYING           | VARCHAR                |
| 15 | TIME WITH TIME ZONE         | TIME                   |
| 16 | TIMESTAMP WITH TIME ZONE    | TIMESTAMP              |
| 17 | TIMESTAMP WITHOUT TIME ZONE | TIMESTAMP              |
| 18 | TIME WITHOUT TIME ZONE      | TIME                   |
| 19 | INTERVAL DAY TO SECOND      | INTERVAL DAY TO SECOND |
| 20 | INTERVAL YEAR TO MONTH      | INTERVAL YEAR TO MONTH |
| 21 | DATE                        | DATE                   |
