### Oracle → YashanDB数据类型映射规则

|  顺序| Oracle数据类型| YashanDB类型| 参数影响| 存在风险|
|----|-----------------------------------------------------------|--------------------------------------------------|-----------------------------------------------------------|----------------------------------------------|
| 1  | VARCHAR2(size [BYTE or CHAR])                             | VARCHAR (size [BYTE or CHAR])                    | size<当前数据库最大size  维持size大小 ,<br/>超过最大size     size= 最大size | size超过YashanDB最大范围                           |
| 2  | NVARCHAR2(N)                                              | NVARCHAR(N)                                      | size<当前数据库最大size    维持size大小，<br/>  超过最大size     size= 最大size | size超过YashanDB最大范围                           |
| 3  | NUMBER[ (p[, s]) ]                                        | NUMBER                                           |                                                           |                                              |
| 4  | FLOAT[(p)]                                                | FLOAT[(p)]                                       | p<=48 FLOAT[P],<br/>p>48 YashanDB的23.1版本转换为NUMBER         |                                              |
| 5  | LONG                                                      | CLOB                                             |                                                           |                                              |
| 6  | DATE                                                      | DATE                                             |                                                           |                                              |
| 7  | BINARY_FLOAT                                              | BINARY_FLOAT                                     |                                                           |                                              |
| 8  | BINARY_DOUBLE                                             | BINARY_DOUBLE                                    |                                                           |                                              |
| 9  | TIMESTAMP[(M)]                                            | TIMESTAMP                                        |                                                           |                                              |
| 10 | TIMESTAMP [(M)]<br/>WITH TIME ZONE                        | 23.4以下：TIMESTAMP                                 |                                                           |                                              |
|    |                                                           | 23.4以上：TIMESTAMP [(M)]<br/> WITH TIME ZONE       |                                                           |                                              |
| 11 | TIMESTAMP [(M)]<br/>WITH LOCAL TIME ZONE                  | 23.4以下：TIMESTAMP                                 |                                                           |                                              |
|    |                                                           | 23.4以上：TIMESTAMP [(M)]<br/> WITH LOCAL TIME ZONE |                                                           |                                              |
| 12 | INTERVAL YEAR [(M)] TO MONTH                              | INTERVAL YEAR [(M)] TO MONTH                     |                                                           |                                              |
| 13 | INTERVAL DAY [(M)] <br/>TO SECOND [(N)]                   | INTERVAL DAY [(M)] <br/>TO SECOND [(N)]          |                                                           | 数值范围不一致                                      |
| 14 | RAW(size)                                                 | RAW(size)                                        |                                                           |                                              |
| 15 | LONG RAW                                                  | BLOB                                             |                                                           |                                              |
| 16 | CHAR [(size [BYTE or CHAR])]                              | CHAR [(size [BYTE or CHAR])]                     |                                                           |                                              |
| 17 | NCHAR[(size)]                                             | NCHAR[size]                                      |                                                           |                                              |
| 18 | CLOB                                                      | VARCHAR/CLOB[默认]                                 |                                                           |                                              |
| 19 | NCLOB                                                     | NVARCHAR/NCLOB[默认]                               |                                                           |                                              |
| 20 | BLOB                                                      | BLOB                                             |                                                           |                                              |
| 21 | JSON                                                      | JSON[默认]/CLOB                                    |                                                           | 超过YashanDB最大范围                               |
| 22 | XMLTYPE                                                   | CLOB                                             |                                                           | 数据迁移暂不支持此类型                                  |
| 23 | INT/INTEGER/SMALLINT/DEC/NUMERIC/decimal <br/>本质上都是NUMBER | NUMBER                                           |                                                           |                                              |
| 24 | DOUBLE PRECISION                                          | NUMBER                                           |                                                           |                                              |
| 25 | ROWID                                                     | VARCHAR(18)                                      | 受配置参数assessment.rowidToVarchar影响                          | 若要迁移，参数export.tool需要选jdbc                    |
| 26 | UROWID[(size)]                                            | VARCHAR[(size)]                                  | 受配置参数assessment.rowidToVarchar影响                          | 若要迁移，参数export.tool需要选jdbc                    |
| 27 | BFILE                                                     | BFILE                                            |                                                           | DIRECTORY支持的最大长度范围不一致 |

### 限制说明

1. 不支持的数据类型：VARYING ARRAY、SDO_GEOMETRY、SDO_TOPO_GEOMETRY、SDO_GEORASTER、HTTPURIType、XDBURIType、DBURIType等数据类型。
2. JSON类型默认映射为JSON，目前仅支持小于等于32KB的数据迁移，如有大于32KB的数据需手动修改映射关系为CLOB。
3. FLOAT类型，如果参数大于48，YashanDB的22.2版本会将其自动兼容为DOUBLE。
4. DATE类型，由于两端数据迁移格式不一致，需要事先调整目标端数据库的日期格式。
   ````sql
   -- 配置需重启后生效
   alter system set date_format='yyyy-mm-dd hh24:mi:ss' scope=spfile;
   ````
5. BFILE类型，仅目标端崖山数据库>=23.4.2版本后支持，若要迁移。需手动将文件复制到目标端对应位置并在目标端数据库创建对应的DIRECTORY对象，例：
   ````sql
   -- 源端：
   CREATE DIRECTORY BFILEDIR AS '/opt/oracle/oradata';
   CREATE TABLE YMP.TABLE1(COL0 INT, COL1 BFILE);
   INSERT INTO YMP.TABLE1 VALUES(1,BFILENAME('BFILEDIR', 'example.txt'));
   
   -- 若进行迁移，需手动将源端机器中的/opt/oracle/oradata/example.txt复制到目标端机器的/opt/oracle/oradata路径下，并在目标端数据库创建对应的DIRECTORY。
   -- 目标端：
   CREATE DIRECTORY BFILEDIR AS '/opt/oracle/oradata';
   ````
