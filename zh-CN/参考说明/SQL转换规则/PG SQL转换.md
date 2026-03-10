本文档基于PG已有语法，根据SQL转换对以下语法进行了规则化的转换。

## 概述

在YMP的任务评估阶段，会对源数据库中的对象（表、视图、约束、索引等）进行兼容性评估，以确保源端数据库对象可以成功迁移到目标数据库。  
在评估阶段，会尝试获取源端数据库对象的DDL并在目标数据库执行。如果执行成功，则说明此对象为原生兼容，无需进行特殊处理。 
否则，会使用SQL转换工具根据源端到目标端数据库的语法转换规则对此DDL进行语法转换，以适配目标数据库的语法特性。  
如果转换成功，会尝试在目标数据库中执行转换后的SQL，执行成功则说明此对象为自动兼容，可以进行迁移。
由于PG没有提供系统的获取对象DDL的方法，所以在评估阶段DDL由YMP平台获取部分元数据信息进行拼接，可能会有部分语法不支持。

## CREATE TABLE

|  序号| PG语法/关键字| YashanDB23转换规则|
|---------|-------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------|
| [1](#1) | [ [ GLOBAL &#124; LOCAL ] { TEMPORARY &#124; TEMP } &#124; UNLOGGED ]                                                               | 保留 TEMPORARY  ， UNLOGGED转为NOLOGGING                | 
| 2       | column_name data_type [ COMPRESSION compression_method \] [ COLLATE collation ]                                       | 删除COLLATE信息                                        | 
| 3       | 表内约束                                                                                                                    | 转为独立约束对象                                           | 
| 4       | GENERATED ALWAYS AS ( generation_expr ) STORED                                                                          | 仅在YashanDB版本>=23.4.2时保留                            | 
| 5       | GENERATED { ALWAYS &#124; BY DEFAULT } AS IDENTITY [ ( sequence_options ) ]                                                 | 修改其默认值为其默认生成的序列名                                   | 
| 6       | PARTITION BY { RANGE &#124; LIST &#124; HASH } ( { column_name &#124; ( expression ) } [ COLLATE collation ] [ opclass ] [, ... ] ) | 只保留YashanDB支持的分区定义信息，由于YashanDB的分区定义需要更多信息，其基本都不兼容 | 
| 7       | WITH ( storage_parameter [= value] [, ... ] )                                                                           | 删除                                                 | 
| 8       | INHERITS ( parent_table [, ... ] )                                                                                      | 不获取，转为获取其全部字段信息                                    | 
| 9       | TABLESPACE \<表空间名>                                                                                                       | 保留                                                 | 

## CREATE SEQUENCE

|  序号| PG语法/关键字| YashanDB23转换规则|
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| [10](#10) | AS data_type                                                                                                                                                            | 删除                    | 
| 11        | [ INCREMENT [ BY ] increment ]  [ MINVALUE minvalue &#124; NO MINVALUE ] [ MAXVALUE maxvalue &#124; NO MAXVALUE ] [ START [ WITH ] start ] [ CACHE cache ] [ [ NO ] CYCLE ] | 全部保留，CACHE如果值等于1则不会保留 | 
| 12        | OWNED BY { table_name.column_name &#124; NONE }                                                                                                                             | 不获取，不保留               | 

## CREATE INDEX

|  序号| PG语法/关键字| YashanDB23转换规则|
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|----------------|
| 13        | [ UNIQUE ]                                                                                                                               | 保留             | 
| 14        | [ CONCURRENTLY ] [ [ IF NOT EXISTS ] name ]                                                                                              | 仅保留name        | 
| 15        | [ ONLY ]                                                                                                                                 | 不获取，不保留        | 
| 16        | [ USING method ]                                                                                                                         | 不保留            | 
| 17        | [ COLLATE collation ] [ opclass [ ( opclass_parameter = value [, ... ] ) ] ] [ ASC &#124; DESC ] [ NULLS { FIRST &#124; LAST } ]                 | 仅保留ASC/DESC    | 
| [18](#18) | [ INCLUDE ( column_name [, ...] ) ] [ WITH ( storage_parameter [= value] [, ... ] ) ] [ TABLESPACE tablespace_name ] [ WHERE predicate ] | 仅保留TABLESPACE  | 

## CREATE VIEW

|  序号| PG语法/关键字| YashanDB23转换规则|
|-----------|-----------------------------------------------------|-----------------|
| 19        | [ OR REPLACE ] [ TEMP &#124; TEMPORARY ] [ RECURSIVE ]  | 不保留             | 
| 20        | [ WITH ( view_option_name [= view_option_value] ) ] | 仅转换CHECK_OPTION | 
| [21](#21) | [ WITH [ CASCADED &#124; LOCAL ] CHECK OPTION ]         | 不保留CASCADED/LOCAL | 

## CREATE MATERIALIZED  VIEW

|  序号| PG语法/关键字| YashanDB23转换规则|
|----|-------------------------------------------------|----------------|
| 22 | WITH ( storage_parameter [= value] [, ... ] ) | 未获取            | 
| 23 | USING method                                    | 未获取            | 
| 24 | WITH [ NO ] DATA                               | 未获取            | 
| 25 | TABLESPACE tablespace_name                      | 保留             | 

## 全部示例

<span id="1" name="1" class="yaslink"></span>

### 示例1

```sql
 -- PG
CREATE TABLE console_4 (
                           id INTEGER NOT NULL DEFAULT NEXTVAL('console_4_id_seq'::regclass),
                           name text NOT NULL,
                           price NUMERIC(10,2) NOT NULL
);
CREATE UNLOGGED TABLE usbit2233 (
    user_id CHARACTER varying
);
 -- YashanDB23预期结果
CREATE TABLE console_4 (
                           id INTEGER DEFAULT console_4_id_seq.NEXTVAL ,
                           name CLOB,
                           price NUMBER(10,2)
);
CREATE TABLE usbit2233( user_id CLOB
) NOLOGGING;

```

<span id="10" name="10" class="yaslink"></span>

### 示例2

```sql
 -- PG
CREATE SEQUENCE  drg.bedday_cof_dic_id_seq
    AS INTEGER
    INCREMENT BY 1 MINVALUE 1  MAXVALUE 2147483647
    START 1  CACHE 1  CYCLE;
-- YashanDB23预期结果
CREATE SEQUENCE drg.bedday_cof_dic_id_seq INCREMENT BY 1 MINVALUE 1 MAXVALUE 2147483647 START WITH 1 CYCLE;
```

<span id="18" name="18" class="yaslink"></span>

### 示例3

```sql
 -- PG
CREATE INDEX idx_02 ON user_01.tb_01 
    USING btree (c1 NULLS FIRST, c2 DESC) 
    INCLUDE (c1, c2) 
    WHERE (c1 > 0)
-- YashanDB23预期结果
CREATE INDEX idx_02 ON user_01.tb_01 (c1,c2 DESC);
```

<span id="21" name="21" class="yaslink"></span>

### 示例4

```sql
 -- PG
CREATE  VIEW user_01.view2  WITH (check_option=cascaded) AS  SELECT tb_01.c1,
    tb_01.c2,
    tb_01.c2_2
    FROM tb_01;
-- YashanDB23预期结果
CREATE VIEW user_01.view2
AS
SELECT tb_01.c1, tb_01.c2, tb_01.c2_2
FROM tb_01
WITH CHECK OPTION;
```
