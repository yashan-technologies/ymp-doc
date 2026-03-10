### Oracle → YashanDB Data Type Mapping Rules

|Order |Oracle Data Type |YashanDB Type |Parameter Effects |Risks |
|----|-----------------------------------------------------------|--------------------------------------------------|-----------------------------------------------------------|----------------------------------------------|
| 1     | VARCHAR2(size [BYTE or CHAR])                             | VARCHAR (size [BYTE or CHAR])                  | size<current database maximum size  Maintain size, <br/> exceeds maximum size    size= maximum size | size exceeds YashanDB maximum range           |
| 2     | NVARCHAR2(N)                                              | NVARCHAR(N)                                    | size<current database maximum size   Maintain size, <br/> exceeds maximum size     size= maximum size | size exceeds YashanDB maximum range           |
| 3  | NUMBER[ (p[, s]) ]                                        | NUMBER                                           |                                                           |                                              |
| 4     | FLOAT[(p)]                                                | FLOAT[(p)]                                     | p<=48 FLOAT[P],<br/>p>48 will be converted to NUMBER in YashanDB version 23.1 |                                                |
| 5  | LONG                                                      | CLOB                                             |                                                           |                                              |
| 6  | DATE                                                      | DATE                                             |                                                           |                                              |
| 7  | BINARY_FLOAT                                              | BINARY_FLOAT                                     |                                                           |                                              |
| 8  | BINARY_DOUBLE                                             | BINARY_DOUBLE                                    |                                                           |                                              |
| 9  | TIMESTAMP[(M)]                                            | TIMESTAMP                                        |                                                           |                                              |
| 10    | TIMESTAMP [(M)]<br/>WITH TIME ZONE                        | For version below 23.4: TIMESTAMP              |                                                               |                                                |
|       |                                                           | For version above 23.4: TIMESTAMP [(M)]<br/> WITH TIME ZONE |                                                               |                                                |
| 11    | TIMESTAMP [(M)]<br/>WITH LOCAL TIME ZONE                  | For version below 23.4: TIMESTAMP              |                                                               |                                                |
|       |                                                           | For version above 23.4: TIMESTAMP [(M)]<br/> WITH LOCAL TIME ZONE |                                                               |                                                |
| 12 | INTERVAL YEAR [(M)] TO MONTH                              | INTERVAL YEAR [(M)] TO MONTH                     |                                                           |                                              |
| 13    | INTERVAL DAY [(M)] <br/>TO SECOND [(N)]                   | INTERVAL DAY [(M)] <br/>TO SECOND [(N)]      |                                                               | Value range inconsistency                       |
| 14 | RAW(size)                                                 | RAW(size)                                        |                                                           |                                              |
| 15 | LONG RAW                                                  | BLOB                                             |                                                           |                                              |
| 16 | CHAR [(size [BYTE or CHAR])]                              | CHAR [(size [BYTE or CHAR])]                     |                                                           |                                              |
| 17 | NCHAR[(size)]                                             | NCHAR[size]                                      |                                                           |                                              |
| 18    | CLOB                                                      | VARCHAR/CLOB[default]                          |                                                               |                                                |
| 19    | NCLOB                                                     | NVARCHAR/NCLOB[default]                        |                                                               |                                                |
| 20 | BLOB                                                      | BLOB                                             |                                                           |                                              |
| 21    | JSON                                                      | JSON[default]/CLOB                             |                                                               | Exceeds YashanDB maximum range                 |
| 22    | XMLTYPE                                                   | CLOB                                           |                                                               | Data migration for this type is not supported yet |
| 23    | INT/INTEGER/SMALLINT/DEC/NUMERIC/decimal <br/>essentially are NUMBER | NUMBER                                         |                                                               |                                                |
| 24 | DOUBLE PRECISION                                          | NUMBER                                           |                                                           |                                              |
| 25    | ROWID                                                     | VARCHAR(18)                                    | Affected by configuration parameter assessment.rowidToVarchar      | For migration, parameter export.tool needs to be jdbc |
| 26    | UROWID[(size)]                                            | VARCHAR[(size)]                                | Affected by configuration parameter assessment.rowidToVarchar      | For migration, parameter export.tool needs to be jdbc |
| 27    | BFILE                                                     | BFILE                                          |                                                               | The maximum length range supported by DIRECTORY is inconsistent |

### Limitations

1. Unsupported data types: VARYING ARRAY, SDO_GEOMETRY, SDO_TOPO_GEOMETRY, SDO_GEORASTER, HTTPURIType, XDBURIType, DBURIType, etc.
2. The JSON type is mapped to JSON by default, currently, it only supports data migration of size less than or equal to 32KB. If the data exceeds 32KB, you need to manually change the mapping to CLOB.
3. For the FLOAT type, if the parameter is greater than 48, YashanDB version 22.2 will automatically handle it as DOUBLE.
4. For the DATE type, due to inconsistent migration formats on both ends, you need to adjust the date format of the target database in advance.
   ````sql
   -- Configuration takes effect after restart
   alter system set date_format='yyyy-mm-dd hh24:mi:ss' scope=spfile;
   ````
5. The BFILE type is only supported in the target YashanDB version >= 23.4.2. For migration, you need to manually copy the file to the corresponding location on the target side and create the corresponding DIRECTORY object in the target database, for example:
   ````sql
   -- Source side:
   CREATE DIRECTORY BFILEDIR AS '/opt/oracle/oradata';
   CREATE TABLE YMP.TABLE1(COL0 INT, COL1 BFILE);
   INSERT INTO YMP.TABLE1 VALUES(1,BFILENAME('BFILEDIR', 'example.txt'));
   
   -- If migrating, you need to manually copy /opt/oracle/oradata/example.txt from the source machine to /opt/oracle/oradata on the target machine, and create the corresponding DIRECTORY in the target database.
   -- Target side:
   CREATE DIRECTORY BFILEDIR AS '/opt/oracle/oradata';
   ````
