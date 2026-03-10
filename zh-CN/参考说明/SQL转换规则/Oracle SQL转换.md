本文档基于oracle21c已有语法，根据SQL转换对以下语法进行了规则化的转换。

## 概述

在YMP的任务评估阶段，会对源数据库中的对象（表、视图、约束、索引等）进行兼容性评估，以确保源端数据库对象可以成功迁移到目标数据库。在评估阶段，会尝试获取源端数据库对象的DDL并在目标数据库执行。如果执行成功，则说明此对象为原生兼容，无需进行特殊处理。否则，会使用SQL转换工具根据源端到目标端数据库的语法转换规则对此DDL进行语法转换，以适配目标数据库的语法特性。如果转换成功，会尝试在目标数据库中执行转换后的SQL，执行成功则说明此对象为自动兼容，可以进行迁移。  

## CREATE TABLE
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------|------------------|
| [1](#1) | constraint_state ::= <br>[ NOT DEFERRABLE] <br>[INITIALLY IMMEDIATE]<br>[ RELY &#124; NORELY ]<br>[ using_index_clause ]<br>[ ENABLE &#124; DISABLE ]                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | 全部保留                             | 同22.2            |
| [2](#2) | using_index_clause::= <br>USING INDEX<br>{(create_index_clause) &#124; index_attr_clause}<br>注：create_index_clause、index_attr_clause参考create index                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | 保留支持的create index、index_attr相关语法 | 同22.2            |
| [3](#3) | segment_attributes_clause=:: <br>logging_clause=::<br>NOLOGGING&#124;FILESYSTEM_LIKE_LOGGING;                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | 保留logging_clause                 | 同22.2            |
| [4](#4) | storage_clause=::<br>STORAGE<br>({<br>NEXT size_clause <br>&#124; MINEXTENTS integer<br> MAXEXTENTS { integer &#124;UNLIMITED } <br>&#124; PCTINCREASE integer <br>&#124; FREELISTS integer<br>&#124; FREELIST GROUPS integer <br>&#124; BUFFER_POOL { KEEP &#124; RECYCLE &#124; DEFAULT }<br>&#124; FLASH_CACHE { KEEP &#124; NONE &#124; DEFAULT }<br>&#124; ( CELL_FLASH_CACHE ( KEEP &#124; NONE &#124; DEFAULT ) )<br>} ...<br>)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | 全部保留                             | 同22.2            |
| [5](#5) | table_properties=::<br>[ read_only_clause ]<br>[ CACHE&#124; NOCACHE ]<br>[ parallel_clause ]                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | 全部保留                             | 同22.2            |
| [6](#6) | LOB_storage_clause=:: <br>LOB <br>{ (LOB_item [, LOB_item ]...) <br>STORE AS { [SECUREFILE &#124; BASICFILE] (LOB_storage_parameters)<br>} <br>&#124; (LOB_item) <br>STORE AS { [SECUREFILE &#124; BASICFILE] (LOB_storage_parameters)<br>} <br>} <br>注：新增支持项为[SECUREFILE &#124; BASICFILE]参数                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | 全部保留                             | 同22.2            |
| [7](#7) | table_partitioning_clauses=:: <br>composite_range_partitions <br>&#124; composite_list_partitions <br>&#124; composite_hash_partitions <br>&#124; consistent_hash_with_subpartitions <br><br>composite_range_partitions=::<br>PARTITION BY RANGE ( column [, column]... )<br>   { subpartition_by_range<br>   &#124; subpartition_by_list<br>   &#124; subpartition_by_hash<br>   }<br> ( range_partition_desc [, range_partition_desc ]... )<br>注：不支持 [ INTERVAL ( expr ) [ STORE IN ( tablespace [, tablespace]... ) ]] <br><br>composite_list_partitions=::<br>PARTITION BY LIST ( column [, column]... )<br>   { subpartition_by_range<br>   &#124; subpartition_by_list<br>   &#124; subpartition_by_hash<br>   }<br> ( list_partition_desc [, list_partition_desc]... )<br>删除[ AUTOMATIC [ STORE IN ( tablespace [, tablespace ]... ) ] ] <br><br>composite_hash_partitions=:: <br>PARTITION BY HASH (column [, column ] ...)<br>   { subpartition_by_range<br>   &#124; subpartition_by_list<br>   &#124; subpartition_by_hash<br>   }<br>   {  hash_partitions_by_quantity}<br>注：不支持individual_hash_partitions &#124;<br><br>consistent_hash_with_subpartitions=::<br>PARTITION BY CONSISTENT HASH (column [, column ]...)<br>   { subpartition_by_range<br>   &#124; subpartition_by_list<br>   &#124; subpartition_by_hash<br>   }<br>   [ PARTITIONS AUTO ] | 22.2无此语法                         | 除不支持部分外全部保留      |
| 8 | identity_clause=:: GENERATED [ ALWAYS &#124; BY DEFAULT [ ON NULL ] ]AS IDENTITY [ ( identity_options ) ] <br> identity_options =:: { START WITH ( integer &#124; LIMIT VALUE ) &#124; INCREMENT BY integer &#124; ( MAXVALUE integer &#124; NOMAXVALUE ) &#124; ( MINVALUE integer &#124; NOMINVALUE ) &#124; ( CYCLE &#124; NOCYCLE ) &#124; ( CACHE integer &#124; NOCACHE ) &#124; ( ORDER &#124; NOORDER ) }...                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 转为自增序列                           | 全部保留             |

## ALTER TABLE
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|----------|--------------------------------------------------------------------------------------------------------------------------|------------------|------------------|
| [9](#8)  | [alter_table_properties] =:: <br>readonly_clause::=[READ ONLY &#124; READ WRITE] <br>&#124;logging_clause=::[LOGGING&#124;NOLOGGING] | 全部保留             | 全部保留             |
| [10](#9) | [truncate_partition_subpart]=:: <br>TURNCATE {PARTITION} {partition_name} <br>[ DROP &#124;REUSE STORAGE] [ CASCADE ]        | 全部保留             | 全部保留             |

## CREATE INDEX
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|--------------------------------------------------------------------------------------------------------------------------------------------------------|------------------|------------------|
| [11](#10) | CREATE [UNIQUE] INDEX [schema.]index_name ON table_index_index_clause {USABLE &#124;&#124; UNUSABLE} <br>注：新增支持{USABLE &#124;&#124; UNUSABLE}                          | 全部保留             | 同22.2            |
| [12](#11) | [table_index_index_clause]=:: <br>[schema.]table (column [ASC&#124;&#124;DESC],..)  <br>[index_properties] <br>注：新增支持项为列的[ASC&#124;&#124;DESC]选项                       | 全部保留             | 同22.2            |
| [13](#12) | [index_attributes]=:: <br>[COMPRESS num &#124;NOCOMPRESS] <br>&#124;[VISIBLE&#124;INVISIBLE]<br>&#124; [PARALLEL num &#124;NOPARALLEL] <br>&#124;[logging&#124;nologging]&#124;REVERSE | 全部保留             | 同22.2            |

## CREATE VIEW
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------|------------------|
| [14](#13) | CREATE [OR REPLACE] [[NO] FORCE] [ EDITIONABLE  &#124; NONEDITIONABLE ]  VIEW [schema "."] view_name AS subquery [with read only]<br>注：新增支持项为 [[NO] FORCE] [ EDITIONABLE  &#124; NONEDITIONABLE ] [with read only] | 全部保留             | 全部保留             |

## CREATE MATERIALIZED VIEW

|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------|------------------|
| [15](#14) | CREATE MATERIALIZED VIEW [ schema. ] materialized_view<br/>[(,{column_alias ...})]<br/>{physical_properties materialized_view_props} <br>[ create_mv_refresh ] <br>[ query_rewrite_clause ] <br>AS subquery ; <br><br>physical_properties=::  <br>segment_attributes_clause::= <br>TABLESPACE tablespace <br><br>materialized_view_props::=  <br>build_clause::= <br>BUILD { IMMEDIATE &#124; DEFERRED } <br><br>create_mv_refresh::= <br>{ REFRESH { { COMPLETE &#124; FORCE } &#124; { ON DEMAND &#124; ON COMMIT }  <br>&#124; { START WITH date &#124; NEXT date }... &#124; NEVER REFRESH }  <br><br>query_rewrite_clause::= <br>{ ENABLE &#124; DISABLE } QUERY REWRITE | YashanDB22.2无此语法 | 全部保留             |

## CREATE USER
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------|
| [16](#15) | CREATE USER user_name <span>{{</span>IDENTIFIED { BY password [ [HTTP] DIGEST { ENABLE &#124; DISABLE } ] &#124; EXTERNALLY [ AS 'certificate_DN' &#124; AS 'kerberos_principal_name' ] &#124; GLOBALLY [ AS '[ directory_DN ]' ] }} &#124; NO AUTHENTICATION} [ DEFAULT COLLATION collation_name ] [ DEFAULT TABLESPACE tablespace &#124; [ LOCAL ] TEMPORARY TABLESPACE { tablespace &#124; tablespace_group_name } &#124; { QUOTA { size_clause &#124; UNLIMITED } ON tablespace }... &#124; PROFILE profile &#124; PASSWORD EXPIRE &#124; ACCOUNT { LOCK &#124; UNLOCK } [ DEFAULT TABLESPACE tablespace &#124; TEMPORARY TABLESPACE { tablespace &#124; tablespace_group_name } &#124; { QUOTA { size_clause &#124; UNLIMITED } ON tablespace }... &#124; PROFILE profile &#124; PASSWORD EXPIRE &#124; ACCOUNT { LOCK &#124; UNLOCK } &#124; ENABLE EDITIONS &#124; CONTAINER = { CURRENT &#124; ALL }]...] | CREATE USER user_name IDENTIFIED BY  "Yasdb123.." |  CREATE USER user_name IDENTIFIED BY  "Yasdb123.."   |

## CREATE TYPE
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|----------------------------------|------------------------------------------------------------------------|------------------|
| [17](#16) | plsql_type_source                | 转换时删除OID/Sharing_clause /default_collation_clause/accessible_by_clause | 同左               |
| [18](#16) | object_type_def/varray_type_spec | 转换时删除(NOT INSTANTIABLE)&#124;(NOT PERSISTABLE)                             | 同左               |
| [19](#18) | element_spec                     | 转换时删除 inheritance_clauses/restrict_references_pragma                   | 同左               |
| [20](#19) | constructor_spec                 | 转换时删除FINAL/ INSTANTIABLE /call_spec                                    | 同左               |

## CREATE TRIGGER
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|-------------------------|-----------------------------------------------|------------------|
| [21](#20) | plsql_trigger_source    | 转换时删除Sharing_clause /default_collation_clause | 同左               |
| 22        | referencing_clause      | 转换时含有parent as 会转换失败                          | 同左               |
| [23](#20) | trigger_edition_clause  | 转换时删除                                         | 同左               |
| 24        | trigger_ordering_clause | 转换时含有 precedes会转换失败                           | 同左               |
| 25        | instead_of_dml_trigger  | 不支持正常转换                                       | 同左               |
| 26        | system_trigger          | 不支持正常转换                                       | 同左               |
| 27        | compound_dml_trigger    | 不支持正常转换                                       | 同左               |

## CREATE FUNCTION
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------|
| [28](#21) | or replace                 | 正常转换                                                                                                                                                                   | 同左               |
| [29](#21) | EDITIONABLE/NONEDITIONABLE | 正常转换                                                                                                                                                                   | 同左               |
| [30](#21) | plsql_function_source      | 转换时删除sharing_clause/accessible_by_clause/default_collation_clause/deterministic_clause/parallel_enable_clause/ result_cache_clause/aggregate_clause/pipelined_clause/sql_macro_clause | 同左               |
| 31        | call_spec                  | 不支持正常转换                                                                                                                                                                | 同左               |

## CREATE PROCEDURE
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------|
| 32        | or replace                 | 正常转换                                                                                                                                                                   | 同左               |
| 33        | EDITIONABLE/NONEDITIONABLE | 正常转换                                                                                                                                                                   | 同左               |
| [34](#22) | plsql_procedure_source     | 转换时删除sharing_clause/accessible_by_clause/default_collation_clause | 同左               |
| 35        | call_spec                  | 不支持正常转换                                                                                                                                                                | 同左               |

## CREATE PACKAGE
|  序号| Oracle语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
|-----------|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------|
| 36        | or replace                 | 正常转换                                                                                                                                                                   | 同左               |
| 37        | EDITIONABLE/NONEDITIONABLE | 正常转换                                                                                                                                                                   | 同左               |
| [38](#23) | plsql_package_source       | 转换时删除sharing_clause/accessible_by_clause/default_collation_clause | 同左               |
| [39](#21) | plsql_package_body_source  | 转换时删除sharing_clause | 同左               |
| 40     | call_spec                  | 不支持正常转换                                                                                                                                                                | 同左               |

<span id="1" name="1" class="yaslink"></span>
### 示例1

 ```sql
-- Oracle 
CREATE TABLE t41 (id INT PRIMARY key NOT DEFERRABLE INITIALLY IMMEDIATE 
noRELY ENABLE VALIDATE )

-- YashanDB22.2预期结果
CREATE TABLE t41 (
id INT PRIMARY KEY NOT DEFERRABLE INITIALLY IMMEDIATE NORELY ENABLE VALIDATE);

-- YashanDB23.1预期结果
-- 同YashanDB22.2
 ```

<span id="2" name="2" class="yaslink"></span>

### 示例2

```sql
-- Oracle
CREATE TABLE teett (
id INT PRIMARY KEY USING INDEX
PCTFREE 1
INITRANS 2 LOGGING ONLINE 
TABLESPACE users REVERSE VISIBLE );

CREATE TABLE fgfg (
id INT PRIMARY KEY USING INDEX(
CREATE INDEX frs_idx ON fgfg ( id ASC ) 
PCTFREE 1
INITRANS 2 LOGGING ONLINE 
TABLESPACE users COMPRESS 1 REVERSE VISIBLE NOPARALLEL) VALIDATE
);

-- YashanDB22.2预期结果
/*The 1 SQL statement*/
CREATE TABLE teett (
 id INT PRIMARY KEY USING INDEX  
 PCTFREE 1
 INITRANS 2 LOGGING ONLINE 
 TABLESPACE users
 REVERSE
);
/*The 2 SQL statement*/
CREATE TABLE fgfg (
id INT PRIMARY KEY USING INDEX  (
CREATE INDEX frs_idx ON fgfg ( id ASC ) 
PCTFREE 1
INITRANS 2 LOGGING ONLINE 
TABLESPACE users COMPRESS 1 REVERSE  VISIBLE NOPARALLEL)
);

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="3" name="3" class="yaslink"></span>

### 示例3

```sql
-- 	Oracle
CREATE TABLE contracts_sec_fs
(
contract_id NUMBER(12),
contract_name VARCHAR2(80),
file_size NUMBER,
orig_file BLOB
)
TABLESPACE users NOLOGGING;

-- YashanDB22.2预期结果
CREATE TABLE contracts_sec_fs (
contract_id NUMBER(12),
contract_name VARCHAR(80),
file_size NUMBER,
orig_file BLOB
)
NOLOGGING
TABLESPACE users;

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="4" name="4" class="yaslink"></span>

### 示例4

```sql
-- 	Oracle
CREATE TABLE t1 (id INT)
STORAGE (
INITIAL 1k NEXT 1m MINEXTENTS 1 MAXEXTENTS 2 
PCTINCREASE 1 MAXSIZE 1m FREELISTS 1
FREELIST GROUPS 1 BUFFER_POOL KEEP FLASH_CACHE KEEP 
CELL_FLASH_CACHE KEEP optimal 1m encrypt)

-- YashanDB22.2预期结果
	
CREATE TABLE t1 (
id INT
)
STORAGE (
INITIAL 1k NEXT 1m
MINEXTENTS 1 MAXEXTENTS 2
PCTINCREASE 1 MAXSIZE 1m
FREELISTS 1 FREELIST GROUPS 1
BUFFER_POOL KEEP
FLASH_CACHE KEEP
CELL_FLASH_CACHE KEEP
);

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="5" name="5" class="yaslink"></span>

### 示例5

```sql
-- 	Oracle
CREATE TABLE t1(id INT, provinces INT)
PARALLEL 3 CACHE read write;

-- YashanDB22.2预期结果
CREATE TABLE t1 (
id INT,
provinces INT
) READWRITE PARALLEL 3 CACHE ;

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="6" name="6" class="yaslink"></span>

### 示例6

```sql
-- 	Oracle
CREATE TABLE employees1(
employee_info INT,
introduce CLOB)
LOB(introduce) STORE AS BASICFILE (TABLESPACE users);

-- YashanDB22.2预期结果
CREATE TABLE employees1 (
employee_info INT,
introduce CLOB
)
LOB (introduce) STORE AS BASICFILE( TABLESPACE users );

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="7" name="7" class="yaslink"></span>

### 示例7

```sql
-- 	Oracle
/*The 1 SQL statement*/
CREATE TABLE composite_sales
( prod_id NUMBER(6)
, cust_id NUMBER
, time_id DATE
, channel_id CHAR(1)
, promo_id NUMBER(6)
, quantity_sold NUMBER(3)
, amount_sold NUMBER(10,2)
) 
PARTITION BY RANGE (time_id)
SUBPARTITION BY HASH (channel_id)
(PARTITION SALES_Q1_1998 VALUES LESS THAN (TO_DATE('01-APR-1998','DD-MON-YYYY')),
PARTITION SALES_Q2_1998 VALUES LESS THAN (TO_DATE('01-JUL-1998','DD-MON-YYYY')),
PARTITION SALES_Q3_1998 VALUES LESS THAN (TO_DATE('01-OCT-1998','DD-MON-YYYY')),
PARTITION SALES_Q4_1998 VALUES LESS THAN (TO_DATE('01-JAN-1999','DD-MON-YYYY')),
PARTITION SALES_Q1_1999 VALUES LESS THAN (TO_DATE('01-APR-1999','DD-MON-YYYY')),
PARTITION SALES_Q2_1999 VALUES LESS THAN (TO_DATE('01-JUL-1999','DD-MON-YYYY')),
PARTITION SALES_Q3_1999 VALUES LESS THAN (TO_DATE('01-OCT-1999','DD-MON-YYYY')),
PARTITION SALES_Q4_1999 VALUES LESS THAN (TO_DATE('01-JAN-2000','DD-MON-YYYY')),
PARTITION SALES_Q1_2000 VALUES LESS THAN (TO_DATE('01-APR-2000','DD-MON-YYYY')),
PARTITION SALES_Q2_2000 VALUES LESS THAN (TO_DATE('01-JUL-2000','DD-MON-YYYY'))
SUBPARTITIONS 8,
PARTITION SALES_Q3_2000 VALUES LESS THAN (TO_DATE('01-OCT-2000','DD-MON-YYYY'))
(SUBPARTITION ch_c,
SUBPARTITION ch_i,
SUBPARTITION ch_p,
SUBPARTITION ch_s,
SUBPARTITION ch_t),
PARTITION SALES_Q4_2000 VALUES LESS THAN (MAXVALUE)
SUBPARTITIONS 4);
/*The 2 SQL statement*/
CREATE TABLE customers_part (
customer_id NUMBER(6),
cust_first_name VARCHAR2(20),
cust_last_name VARCHAR2(20),
nls_territory VARCHAR2(30),
credit_limit NUMBER(9,2))
PARTITION BY RANGE (credit_limit)
SUBPARTITION BY LIST (nls_territory)
SUBPARTITION TEMPLATE
(SUBPARTITION east VALUES
('CHINA', 'JAPAN', 'INDIA', 'THAILAND'),
SUBPARTITION west VALUES
('AMERICA', 'GERMANY', 'ITALY', 'SWITZERLAND'),
SUBPARTITION other VALUES (DEFAULT))
(PARTITION p1 VALUES LESS THAN (1000),
PARTITION p2 VALUES LESS THAN (2500),
PARTITION p3 VALUES LESS THAN (MAXVALUE));

-- YashanDB22.2预期结果
-- YashanDB22.2无此语法

-- YashanDB23.1预期结果
/*The 1 SQL statement*/
CREATE TABLE composite_sales (
 prod_id NUMBER(6),
 cust_id NUMBER,
 time_id DATE,
 channel_id CHAR(1),
 promo_id NUMBER(6),
 quantity_sold NUMBER(3),
 amount_sold NUMBER(10,2)
)
 PARTITION BY RANGE (time_id) SUBPARTITION BY HASH (channel_id)
 (
  PARTITION SALES_Q1_1998 VALUES LESS THAN (TO_DATE('01-APR-1998', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q2_1998 VALUES LESS THAN (TO_DATE('01-JUL-1998', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q3_1998 VALUES LESS THAN (TO_DATE('01-OCT-1998', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q4_1998 VALUES LESS THAN (TO_DATE('01-JAN-1999', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q1_1999 VALUES LESS THAN (TO_DATE('01-APR-1999', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q2_1999 VALUES LESS THAN (TO_DATE('01-JUL-1999', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q3_1999 VALUES LESS THAN (TO_DATE('01-OCT-1999', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q4_1999 VALUES LESS THAN (TO_DATE('01-JAN-2000', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q1_2000 VALUES LESS THAN (TO_DATE('01-APR-2000', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q2_2000 VALUES LESS THAN (TO_DATE('01-JUL-2000', 'DD-MON-YYYY')) ,
  PARTITION SALES_Q3_2000 VALUES LESS THAN (TO_DATE('01-OCT-2000', 'DD-MON-YYYY'))  (
 SUBPARTITION ch_c,
 SUBPARTITION ch_i,
 SUBPARTITION ch_p,
 SUBPARTITION ch_s,
 SUBPARTITION ch_t
 ),
  PARTITION SALES_Q4_2000 VALUES LESS THAN (MAXVALUE) );

/*The 2 SQL statement*/
CREATE TABLE customers_part (
 customer_id NUMBER(6),
 cust_first_name VARCHAR(20),
 cust_last_name VARCHAR(20),
 nls_territory VARCHAR(30),
 credit_limit NUMBER(9,2)
)
 PARTITION BY RANGE (credit_limit) SUBPARTITION BY LIST (nls_territory)
 SUBPARTITION TEMPLATE (
 SUBPARTITION east VALUES ('CHINA', 'JAPAN', 'INDIA', 'THAILAND'),
 SUBPARTITION west VALUES ('AMERICA', 'GERMANY', 'ITALY', 'SWITZERLAND'),
 SUBPARTITION other VALUES (DEFAULT)
 )
 (
  PARTITION p1 VALUES LESS THAN (1000) ,
  PARTITION p2 VALUES LESS THAN (2500) ,
  PARTITION p3 VALUES LESS THAN (MAXVALUE) );
```

<span id="8" name="8" class="yaslink"></span>

### 示例8

```sql
-- 	Oracle
ALTER TABLE customers_part LOGGING read ONLY

-- YashanDB22.2预期结果
ALTER TABLE customers_part LOGGING READONLY;

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="9" name="9" class="yaslink"></span>

### 示例9

```sql
-- 	Oracle
ALTER TABLE customers_part TRUNCATE PARTITION p1,p2 DROP STORAGE CASCADE;

-- YashanDB22.2预期结果
ALTER TABLE customers_part TRUNCATE PARTITION p1,p2 DROP STORAGE CASCADE;

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="10" name="10" class="yaslink"></span>

### 示例10

```sql
-- 	Oracle
CREATE INDEX NEWTABLE_COLUMN1_IDX ON TEST.NEWTABLE (COLUMN1) UNUSABLE;

-- YashanDB22.2预期结果
CREATE INDEX NEWTABLE_COLUMN1_IDX ON TEST.NEWTABLE ( COLUMN1 ) UNUSABLE;

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="11" name="11" class="yaslink"></span>

### 示例11

```sql
-- 	Oracle
CREATE INDEX NEWTABLE_COLUMN1_IDX ON NEWTABLE (COLUMN1 ASC,column2 DESC);

-- YashanDB22.2预期结果
CREATE INDEX NEWTABLE_COLUMN1_IDX ON NEWTABLE ( COLUMN1 ASC, column2 DESC );

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="12" name="12" class="yaslink"></span>

### 示例12

```sql
-- 	Oracle
CREATE INDEX NEWTABLE_COLUMN1_IDX ON NEWTABLE (COLUMN1 ASC,column2) 
TABLESPACE users INITRANS 2 PCTFREE 1 ONLINE
COMPRESS 1 LOGGING REVERSE VISIBLE NOPARALLEL

-- YashanDB22.2预期结果
CREATE INDEX NEWTABLE_COLUMN1_IDX ON NEWTABLE ( COLUMN1 ASC, column2 )
PCTFREE 1
INITRANS 2 LOGGING ONLINE
TABLESPACE users COMPRESS 1 REVERSE VISIBLE NOPARALLEL

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="13" name="13" class="yaslink"></span>

### 示例13

```sql
-- 	Oracle
CREATE OR replace FORCE NONEDITIONABLE VIEW v111 
AS SELECT 1 a FROM dual WITH READ ONLY 

-- YashanDB22.2预期结果
CREATE OR REPLACE FORCE NONEDITIONABLE VIEW v111
AS
SELECT 1 AS a
FROM dual WITH READ ONLY;

-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="14" name="14" class="yaslink"></span>

### 示例14

```sql
-- 	Oracle
CREATE MATERIALIZED VIEW dqh.mv_subquery TABLESPACE users
BUILD DEFERRED REFRESH COMPLETE ON demand NEXT SYSDATE + 10 / 2460
DISABLE QUERY REWRITE AS SELECT 1 FROM dual

-- YashanDB22.2预期结果
-- YashanDB22.2无此语法

-- YashanDB23.1预期结果
CREATE MATERIALIZED VIEW dqh.mv_subquery TABLESPACE users
BUILD DEFERRED REFRESH COMPLETE ON DEMAND NEXT SYSDATE + 10 / 2460
DISABLE QUERY REWRITE 
AS 
SELECT 1
FROM dual
```

<span id="15" name="15" class="yaslink"></span>

### 示例15

```sql
--Oracle
CREATE USER ymp IDENTIFIED BY ymp_password PASSWORD EXPIRE
       
-- YashanDB预期结果
CREATE USER ymp IDENTIFIED BY "Yasdb123.."
```

<span id="16" name="16" class="yaslink"></span>
### 示例16

```sql
--Oracle
CREATE OR replace EDITIONABLE TYPE a.b FORCE oid 'oid'  sharing =  none
DEFAULT collation using_nls_comp  
authid  definer
accessible BY (
	FUNCTION  a.b,
	PROCEDURE b.c,
	PACKAGE d.e,
	TRIGGER f.g,
	TYPE h.j
)
under a.typeb (
		a VARCHAR(200) )
	 NOT instantiable ;
-- YashanDB22.2预期结果
CREATE OR REPLACE EDITIONABLE TYPE a.b FORCE  AUTHID  CURRENT_USER 
 UNDER a.typeb (
	a VARCHAR(200)
);
-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="18" name="18" class="yaslink"></span>
### 示例18

```sql
--Oracle
CREATE OR replace EDITIONABLE TYPE body a.v sharing = metadata IS 
 map member FUNCTION func ( a INT  )  RETURN VARCHAR  
  pipelined AS BEGIN END ;  ,
 map member FUNCTION func ( a INT  )  RETURN VARCHAR  
  pipelined AS BEGIN END ;
end;
-- YashanDB22.2预期结果
CREATE OR REPLACE EDITIONABLE TYPE BODY a.v IS
	MAP MEMBER FUNCTION func (a INT) RETURN VARCHAR
	IS
	BEGIN
	END;
	MAP MEMBER FUNCTION func (a INT) RETURN VARCHAR
	IS
	BEGIN
	END;
END;
-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="19" name="19" class="yaslink"></span>
### 示例19

```sql
--Oracle
CREATE TYPE YMP_2854_TYPE_CONVERT.employee_constructor_typ AS OBJECT (
  employee_id   NUMBER,
  first_name    VARCHAR2(50),
  last_name     VARCHAR2(50),
  hire_date     DATE,
  CONSTRUCTOR FUNCTION VARCHAR (p_id NUMBER, p_first_name VARCHAR2, p_last_name VARCHAR2, p_hire_date DATE)
  RETURN SELF AS RESULT AS LANGUAGE JAVA NAME string,
  PRAGMA RESTRICT_REFERENCES (DEFAULT, RNDS,WNDS)
) NOT PERSISTABLE;
-- YashanDB22.2预期结果
CREATE  TYPE YMP_2854_TYPE_CONVERT.employee_constructor_typ
 AS OBJECT (
	employee_id NUMBER, 
	first_name VARCHAR(50), 
	last_name VARCHAR(50), 
	hire_date DATE, 
	 CONSTRUCTOR FUNCTION VARCHAR (p_id NUMBER, p_first_name VARCHAR, p_last_name VARCHAR, p_hire_date DATE) RETURN SELF AS RESULT 
);
-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="20" name="20" class="yaslink"></span>
### 示例20

```sql
--Oracle
CREATE OR replace EDITIONABLE TRIGGER t.t1  sharing =metadata DEFAULT collation using_nls_comp
BEFORE
DELETE OR INSERT  OR UPDATE OR UPDATE OF col,col2 ON xxx.tttt
referencing old AS old  NEW AS NEW 
FOR each row 
forward crossedition
follows  tt.t2
ENABLE
WHEN( 1=1)
BEGIN END ;
-- YashanDB22.2预期结果
CREATE OR REPLACE EDITIONABLE TRIGGER t.t1
BEFORE
 DELETE OR INSERT OR UPDATE OR UPDATE OF col,col2 ON xxx.tttt
REFERENCING  OLD AS old NEW AS NEW
FOR EACH ROW 
 FOLLOWS tt.t2
WHEN 1 = 1
BEGIN
END;
-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="21" name="21" class="yaslink"></span>
### 示例21

```sql
-- oracle
CREATE OR REPLACE EDITIONABLE 
FUNCTION FUNCTION_RESULT_CACHE
(in_parameter1 IN VARCHAR2,in_parameter2 IN VARCHAR2) 
RETURN VARCHAR2 RESULT_CACHE
RELIES_ON() 
AS result VARCHAR2(50);
BEGIN 
result := in_parameter1 || '_' || in_parameter2; 
RETURN result; 
END;
-- YashanDB22.2预期结果
CREATE OR REPLACE EDITIONABLE FUNCTION FUNCTION_RESULT_CACHE (
  in_parameter1 IN VARCHAR,
  in_parameter2 IN VARCHAR
)
RETURN VARCHAR
AS
result VARCHAR(50);
BEGIN
  result := in_parameter1 || '_' || in_parameter2;
  RETURN result;
END;
-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="22" name="22" class="yaslink"></span>
### 示例22

```sql
-- oracle
CREATE EDITIONABLE PROCEDURE PROCEDURE1 ( COL0 INT ) 
            AUTHID CURRENT_USER AS SALARY_NEW INT; APPRAISE_NEW CHAR ( 1 ); CASE_NUM CONSTANT INT := 1;
            DONE CONSTANT BOOLEAN := TRUE; BOOL BOOLEAN DEFAULT FALSE; CURSOR C ( JOB VARCHAR2 ) IS 
            SELECT SALARY, APPRAISE FROM EMPLOYEES WHERE JOB = JOB; BEGIN OPEN C ( 'DEPLOYMENT' ); 
            WHILE DONE LOOP FETCH C INTO SALARY_NEW, APPRAISE_NEW; EXIT WHEN C % NOTFOUND; 
            CASE WHEN CASE_NUM > 0 THEN BOOL := TRUE; ELSE BOOL := FALSE; END CASE; END LOOP; CLOSE C; END;
-- YashanDB22.2预期结果
CREATE PROCEDURE PROCEDURE1 (
  COL0 INT
) AUTHID  CURRENT_USER 
AS
  SALARY_NEW INT;
  APPRAISE_NEW CHAR(1);
  CASE_NUM CONSTANT INT := 1;
  DONE CONSTANT BOOLEAN := true;
  BOOL BOOLEAN := false;
  CURSOR C(JOB VARCHAR) IS
    SELECT SALARY, APPRAISE
    FROM EMPLOYEES
    WHERE JOB = JOB;
BEGIN
  OPEN C('DEPLOYMENT');
  WHILE DONE LOOP
  FETCH C INTO SALARY_NEW, APPRAISE_NEW;
  EXIT WHEN C % NOTFOUND;
  CASE
    WHEN CASE_NUM > 0 THEN BOOL := true;
    ELSE BOOL := false;
  END CASE;
  END LOOP;
  CLOSE C;
END;
-- YashanDB23.1预期结果
-- 同YashanDB22.2
```

<span id="23" name="23" class="yaslink"></span>
### 示例23

```sql
-- oracle
CREATE OR REPLACE EDITIONABLE PACKAGE PACKAGE1
AUTHID CURRENT_USER 
ACCESSIBLE BY(FUNCTION FUNCTION1)
AS PACKAGE_SUMMARY INT := 0;
TYPE PACKAGE_AVG IS RECORD(COL0 INT);
END PACKAGE1;
-- YashanDB22.2预期结果
CREATE OR REPLACE EDITIONABLE PACKAGE PACKAGE1 AUTHID  CURRENT_USER  AS 
	PACKAGE_SUMMARY INT := 0;
	TYPE PACKAGE_AVG IS RECORD(
		COL0 INT
	);
END PACKAGE1;
-- YashanDB23.1预期结果
-- 同YashanDB22.2
```
