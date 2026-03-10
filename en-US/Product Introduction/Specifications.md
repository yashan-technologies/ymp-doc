## Database Version Support

### Supported Source Database Types and Versions

|Database Type |Supported Versions (Validated) |Theoretical Compatible Versions (Not Fully Verified) |Description |
| :--------- | :---------------------- | :---------------------------- | :----------------------------------------------------------- |
| Oracle        | 9.2.0.8.0 (Standalone)          | All versions of 9i (Standalone)                |                                                   |
|               | 11.2.0.1.0 (Standalone)         | All versions of 11g (Including Standalone, RAC) |                                                   |
|               | 12.2.0.1.0 (Standalone)         | All versions of 12c (Including Standalone, RAC) |                                                   |
|               | 19.0.0.0.0 (Standalone, RAC)    | All versions of 19c (Including Standalone, RAC) |                                                   |
|               | 21.0.0.0.0 (Standalone)         | All versions of 21c (Including Standalone, RAC) |                                                   |
| MySQL         | 5.6.51 (Standalone)             | All versions of 5.6 (Standalone)               |                                                   |
|               | 5.7.42 (Standalone)             | All versions of 5.7 (Standalone)               |                                                   |
|               | 8.0.23 (Standalone)             | All versions of 8.0 (Standalone)               |                                                   |
| DM            | DM8.1.3.26 (Standalone)         | All versions of DM8 (Standalone)               | DM database currently only supports native mode (0) and MySQL mode (4). |
| YashanDB      | 23.2.5.100 (Standalone)         | Versions 23.2.4.100 and above (Standalone)     | The tools used by YashanDB for source (exp) version come from the installation package of --db in the deployment instructions, ensure compatibility with the source YashanDB version before use. |
|               | 23.3.1.100 (Standalone)         | All versions of 23.3 (Standalone)               | Same as above.                                   |
|               | 23.4.2.100 (Standalone)         | All versions of 23.4 (Standalone)               | Same as above.                                   |
| DB2           | 10.5                                 | 10.5                                              | DB2 as a source is currently an experimental feature, not recommended for use in a production environment. |
|            | 9.5                     | 9.5                           |                                                              |
| PostgreSQL    | 15.8                                 | 15.8                                              | PG as a source is currently an experimental feature, not recommended for use in a production environment. |
|            | 14.13                   | 14.13                         |                                                              |
|            | 9.6                     | 9.6                           |                                                              |

### Supported Target Database Types and Versions

|Database Type |Supported Versions (Validated) |Theoretical Compatible Versions (Not Fully Verified) |
| :--------- | :---------------------- | :------------------------- |
| YashanDB      | 22.2.14.100 (Standalone)        | All versions of 22.2 (Standalone, YAC)       |
|               | 23.2.5.100 (Standalone, YAC)    | All versions of 23.2 (Standalone, YAC)       |
|               | 23.3.1.100 (Standalone, YAC)    | All versions of 23.3 (Standalone, YAC)       |
|               | 23.4.2.100 (Standalone, YAC)    | All versions of 23.4 (Standalone, YAC)       |

> **Note**:
> 
> The theoretical compatible versions have not undergone complete functionality verification and may require compatibility adaptation.
> 
> Currently, YashanDB (MySQL mode) only supports MySQL as the data source.

## Supported Source Database Data Types

|Database |Supported Data Types |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Oracle     | CHAR、NCHAR、VARCHAR2、NVARCHAR2、NUMBER、FLOAT、INT、INTEGER、SMALLINT、DEC、NUMBERIC、DECIMAL、DOUBLE PRECISION、DATE、TIMESTAMP、TIMESTAMP WITH TIME ZONE、TIMESTAMP WITH LOCAL TIME ZONE、INTERVAL YEAR TO MONTH、INTERVAL DAY TO SECOND、LONG、RAW、LONG RAW、CLOB、NCLOB、BLOB、JSON                                                                                                                                                                                                                                                                                                                                                |
| MySQL      | TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT、DECIMAL、FLOAT、DOUBLE、TINYINT UNSGINED、SMALLINT UNSIGNED、MEDIUMINT UNSIGNED、INT UNSIGNED、BIGINT UNSIGNED、CHAR、VARCHAR、DATE、DATETIME、TIMESTAMP、TIME、YEAR、BINARY、VARBINARY、TINYBLOB、TINYTEXT、BLOB、TEXT、MEDIUMBLOB、MEDIUMTEXT、LONGBLOB、LONGTEXT、JSON、BIT、ENUM、SET                                                                                                                                                                                                                                                                                                              |
| DM         | TINYINT、SMALLINT、INT、INTEGER、BIGINT、DEC、DECIMAL、REAL、FLOAT、DOUBLE、DOUBLE PRECISION、NUMBER、NUMERIC、CHAR、CHARACTER、VARCHAR、VARCHAR2、NVARCHAR、NVARCHAR2、NCHAR、DATE、TIME、DATETIME、TIMESTAMP、TIME WITH TIME ZONE、TIMESTAMP WITH LOCAL TIME ZONE、TIMESTAMP WITH TIME ZONE、DATETIME WITH TIME ZONE、INTERVAL DAY TO SECOND、INTERVAL YEAR TO MONTH、BINARY、BLOB、CLOB、IMAGE、LONG、VARBINARY、LONGVARBINARY、LONGVARCHAR、RAW、TEXT、BIT                                                                                                                                                                                           |
| DB2        | SMALLINT、INTEGER、BIGINT、NUMERIC、DOUBLE PRECISION、REAL、DECIMAL、FLOAT、BOOLEAN、CHAR、VARCHAR、CLOB、BINARY、VARBINARY、BLOB、TIME、TIMESTAMP                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| PostgreSQL | SMALLINT、INTEGER、BIGINT、NUMERIC、DOUBLE PRECISION、REAL、MONEY、BOOLEAN、SMALLSERIAL、SERIAL、BIGSERIAL、BIT、BIT VARYING、CHARACTER、CHARACTER VARYING、BPCHAR、BYTEA、TEXT、JSON、JSONB、XML、INET、CIDR、MACADDR、UUID、TIME、TIME WITH TIME ZONE、TIME WITHOUT TIME ZONE、TIMESTAMP、TIMESTAMP WITH TIME ZONE、TIMESTAMP WITHOUT TIME ZONE、DATE、INTERVAL、INTERVAL YEAR、INTERVAL MONTH、INTERVAL DAY、INTERVAL HOUR、INTERVAL MINUTE、INTERVAL SECOND、INTERVAL YEAR TO MONTH、INTERVAL DAY TO HOUR、INTERVAL DAY TO MINUTE、INTERVAL DAY TO SECOND、INTERVAL HOUR TO MINUTE、INTERVAL HOUR TO SECOND、INTERVAL MINUTE TO SECOND、GEOMETRY、GEOGRAPHY |
