### MySQL → YashanDB数据类型映射规则

|  顺序| MySQL数据类型| YashanDB类型| 参数影响| 存在风险|
|----|---------------------|--------------------|---------------------------|---------------------------------------------|
| 1  | TINYINT             | TINYINT            |                           |                                             |
| 2  | SMALLINT            | SMALLINT           |                           |                                             |
| 3  | MEDIUMINT           | INT                |                           |                                             |
| 4  | INT                 | INT                |                           |                                             |
| 5  | BIGINT              | BIGINT             |                           |                                             |
| 6  | DECIMAL             | NUMBER             | 超出NUMBER范围会取NUMBER最大值进行截断 | 源端范围可能大于NUMBER最大值                           |
| 7  | FLOAT               | FLOAT              |                           |                                             |
| 8  | DOUBLE              | DOUBLE             |                           |                                             |
| 9  | BIT [(M)]           | BIT [(M)]          |                           | 使用的Yasldr版本为23.2.4以上才支持迁移，低版本的Yasldr可替换后迁移。 |
| 10 | DATE                | DATE               |                           |                                             |
| 11 | DATETIME            | TIMESTAMP          |                           |                                             |
| 12 | TIMESTAMP[(M)]      | TIMESTAMP[(M)]     |                           |                                             |
| 13 | TIME                | 23.4以下：VARCHAR(17) |                           | 目标端与源端范围不一致                                 |
|    |                     | 23.4以上：TIME        |                           |                                             |
| 14 | YEAR                | SMALLINT           |                           |                                             |
| 15 | CHAR(M)             | CHAR(M CHAR)       |                           |                                             |
| 16 | VARCHAR(M)          | VARCHAR(M CHAR)    |                           | M的长度大于YashanDB最大长度                          |
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
| 27 | JSON                | JSON               |                           | 长度大于YashanDB最大长度                            |
| 28 | ENUM                | VARCHAR(1024)      |                           |                                             |
| 29 | SET                 | VARCHAR(32000)     |                           | 长度大于YashanDB最大长度                            |
| 30 | TINYINT UNSIGNED    | SMALLINT           |                           |                                             |
| 31 | SMALLINT UNSIGNED   | INTEGER            |                           |                                             |
| 32 | MEDIUMINT  UNSIGNED | INTEGER            |                           |                                             |
| 33 | INT  UNSIGNED       | BIGINT             |                           |                                             |
| 34 | BIGINT  UNSIGNED    | NUMBER             |                           |                                             |

### 限制说明

1. 不支持数据类型：GEOMETRY、POINT、LINESTRING、POLYGON、MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、GEOMETRYCOLLECTION等数据类型。
2. JSON类型，目前仅支持小于等于32KB的JSON类型数据迁移，大于32KB的需手动修改映射关系为CLOB。
3. DATE、DATETIME、TIMESTAMP类型数据的迁移值以MySQL服务端默认时区为基准。
