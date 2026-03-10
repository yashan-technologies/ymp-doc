### PG->YashanDB数据类型映射规则以及支持情况

|  顺序| PG源类型| YashanDB映射类型| 评估支持情况| 数据迁移支持情况| 数据校验支持情况| 说明|
|----|-----------------------------------------|-----------------------------------|--------|----------|----------|------------------------------------------------|
| 1  | SMALLINT（别名INT2）                        | SMALLINT                          | 原生兼容   | 支持       | 支持       |                                                |
| 2  | INTEGER（别名INT、INT4）                     | INT                               | 原生兼容   | 支持       | 支持       |                                                |
| 3  | BIGINT（别名INT8）                          | BIGINT                            | 原生兼容   | 支持       | 支持       |                                                |
| 4  | NUMERIC（别名DECIMAL）                      | NUMBER                            | 原生兼容   | 支持       | 支持       |                                                |
| 5  | DOUBLE PRECISION（别名FLOAT8）              | DOUBLE                            | 自动兼容   | 支持       | 支持       |                                                |
| 6  | REAL（别名FLOAT4）                          | REAL                              | 原生兼容   | 支持       | 支持       |                                                |
| 7  | MONEY                                   | NUMBER                            | 自动兼容   | 支持       | 不支持      |                                                |
| 8  | BOOLEAN（别名BOOL）                         | BOOLEAN                           | 原生兼容   | 支持       | 支持       |                                                |
| 9  | SMALLSERIAL（别名SERIAL2）                  | SMALLINT+自建序列                     | 自动兼容   | 支持       | 支持       |                                                |
| 10 | SERIAL（别名SERIAL4）                       | INT+自建序列                          | 自动兼容   | 支持       | 支持       |                                                |
| 11 | BIGSERIAL（别名SERIAL8）                    | BIGINT+自建序列                       | 自动兼容   | 支持       | 支持       |                                                |
| 12 | BIT                                     | CHAR：长度超过8000，则限制到8000            | 自动兼容   | 支持       | 不支持      | PG的BIT长度可达到Integer的最大值，超过YashanDB范围            |
| 13 | BIT VARYING（别名VARBIT）                   | VARCHAR or CLOB：长度超过32000，则转为CLOB | 自动兼容   | 支持       | 不支持      | PG的BIT VARYING长度可达到Integer的最大值，超过YashanDB范围    |
| 14 | CHARACTER（别名CHAR）                       | CHAR or CLOB:长度超过8000，则转为CLOB     | 自动兼容   | 支持       | 支持       | PG的CHARACTER长度可达到10485760，超出YashanDB范围         |
| 15 | CHARACTER VARYING（别名VARCHAR）            | VARCHAR or CLOB:长度超过32000，则转为CLOB | 自动兼容   | 支持       | 支持       | PG的CHARACTER VARYING长度可达到10485760，超出YashanDB范围 |
| 16 | BPCHAR                                  | CHAR or CLOB:长度超过8000，则转为CLOB     | 自动兼容   | 支持       | 支持       | PG的BPCHAR长度可达到10485760，超出YashanDB范围            |
| 17 | BYTEA                                   | BLOB                              | 自动兼容   | 支持       | 支持       | 可能超过YashanDB范围                                 |
| 18 | TEXT                                    | CLOB                              | 自动兼容   | 支持       | 支持       |                                                |
| 19 | JSON                                    | JSON                              | 原生兼容   | 支持       | 不支持      |                                                |
| 20 | JSONB                                   | JSON                              | 自动兼容   | 支持       | 不支持      |                                                |
| 21 | XML                                     | XMLTYPE                           | 自动兼容   | 支持       | 不支持      | 目标端崖山数据库>=23.4.2.100版本，暂不支持数据迁移到xmltype类型      |
| 22 | INET                                    | VARCHAR(100)                      | 自动兼容   | 支持       | 不支持      |                                                |
| 23 | CIDR                                    | VARCHAR(100)                      | 自动兼容   | 支持       | 不支持      |                                                |
| 24 | MACADDR                                 | VARCHAR(100)                      | 自动兼容   | 支持       | 不支持      |                                                |
| 25 | UUID                                    | VARCHAR(36)                       | 自动兼容   | 支持       | 不支持      |                                                |
| 26 | TIME                                    | TIME                              | 原生兼容   | 支持       | 支持       |                                                |
| 27 | TIME WITH TIME ZONE（别名TIMEZ）            | TIME                              | 自动兼容   | 支持       | 支持       |                                                |
| 28 | TIME WITHOUT TIME ZONE                  | TIME                              | 自动兼容   | 支持       | 支持       |                                                |
| 29 | TIMESTAMP                               | TIMESTAMP                         | 原生兼容   | 支持       | 支持       |                                                |
| 30 | TIMESTAMP WITH TIME ZONE（别名TIMESTAMPTZ） | 23.4以下：TIMESTAMP                  | 自动兼容   | 支持       | 支持       | PG支持公元前，超过YashanDB范围                           |
|    |                                         | 23.4以上：TIMESTAMP WITH TIME ZONE   | 原生兼容   | 支持       | 支持       | PG支持公元前，超过YashanDB范围                           |
| 31 | TIMESTAMP WITHOUT TIME ZONE             | TIMESTAMP                         | 自动兼容   | 支持       | 支持       | PG支持公元前，超过YashanDB范围                           |
| 32 | DATE                                    | DATE                              | 原生兼容   | 支持       | 支持       | PG支持公元前，超过YashanDB范围                           |
| 33 | INTERVAL                                | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 34 | INTERVAL YEAR                           | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 35 | INTERVAL MONTH                          | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 36 | INTERVAL DAY                            | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 37 | INTERVAL HOUR                           | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 38 | INTERVAL MINUTE                         | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 39 | INTERVAL SECOND                         | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 40 | INTERVAL YEAR TO MONTH                  | INTERVAL YEAR TO MONTH            | 自动兼容   | 支持       | 支持       |                                                |
| 41 | INTERVAL DAY TO HOUR                    | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 42 | INTERVAL DAY TO MINUTE                  | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 43 | INTERVAL DAY TO SECOND                  | INTERVAL DAY TO SECOND            | 自动兼容   | 支持       | 支持       |                                                |
| 44 | INTERVAL HOUR TO MINUTE                 | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 45 | INTERVAL HOUR TO SECOND                 | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 46 | INTERVAL MINUTE TO SECOND               | INTERVAL DAY TO SECOND            | 自动兼容   | 不支持      | 不支持      |                                                |
| 47 | GEOMETRY                                | ST_GEOMETRY                       | 自动兼容   | 支持       | 支持       |                                                |
| 48 | GEOGRAPHY                               | ST_GEOMETRY                       | 自动兼容   | 支持       | 支持       |                                                |

### 限制说明

1. 其他不支持类型：POINT、LINE、LSEG、BOX、PATH、POLYGON、CIRCLE和其他未提及类型。
