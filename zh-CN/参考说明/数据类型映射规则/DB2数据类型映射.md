### DB2->YashanDB数据类型映射规则以及支持情况

|  顺序| DB2源类型| YashanDB映射类型| 评估支持情况| 数据迁移支持情况| 数据校验支持情况| 说明|
|----|----------------------------|--------------|--------|----------|--|-----------------------------|
| 1  | SMALLINT                   | SMALLINT     | 原生兼容   | 支持       | |                             |
| 2  | INTEGER（别名INT）             | INTEGER      | 原生兼容   | 支持       | |                             |
| 3  | BIGINT                     | BIGINT       | 原生兼容   | 支持       | |                             |
| 4  | NUMERIC                    | NUMBER       | 原生兼容   | 支持       |  |                             |
| 5  | DOUBLE PRECISION（别名DOUBLE） | DOUBLE       | 自动兼容   | 支持       | |                             |
| 6  | REAL                       | REAL         | 原生兼容   | 不支持      |  |                             |
| 7  | DECIMAL                    | NUMBER       | 自动兼容   | 支持       |  |                             |
| 8  | FLOAT                      | FLOAT        | 原生兼容   | 不支持          |  |                             |
| 9  | BOOLEAN                    | BOOLEAN      | 原生兼容   | 不支持          |  |                             |
| 10 | CHAR                       | CHAR         | 原生兼容   | 支持       | |                             |
| 11 | VARCHAR                    | VARCHAR      | 原生兼容   | 支持       | | VARCHAR：长度超过32000，则限制到32000 |
| 12 | CLOB                       | CLOB         | 原生兼容   | 支持       | |                             |
| 13 | BINARY                     | BINARY       | 原生兼容   | 不支持          |  |                             |
| 14 | VARBINARY                  | VARBINARY    | 原生兼容   | 不支持          |  |                             |
| 15 | BLOB                       | BLOB         | 原生兼容   | 支持       | |                             |
| 16 | TIME                       | TIME         | 原生兼容   | 不支持          |  |                             |
| 17 | TIMESTAMP                  | TIMESTAMP    | 原生兼容   | 支持       | |                             |
| 18 | DATE                       | DATE         | 原生兼容   | 不支持          |  |                             |
| 19 | LONG VARCHAR               | CLOB         | 自动兼容   | 支持         |  |                             |
| 20 | LONG VARGRAPHIC            | CLOB         | 自动兼容   | 支持         |  |                             |

### 限制说明

1. 其他不支持类型：REFERENCE、DECFLOAT、CURSOR和其他未提及类型。
