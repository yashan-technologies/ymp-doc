本文档基于MySQL8.0已有语法，根据SQL转换对以下语法进行了规则化的转换。

## 概述

在YMP的任务评估阶段，会对源数据库中的对象（表、视图、约束、索引等）进行兼容性评估，以确保源端数据库对象可以成功迁移到目标数据库。在评估阶段，会尝试获取源端数据库对象的DDL并在目标数据库执行。如果执行成功，则说明此对象为原生兼容，无需进行特殊处理。否则，会使用SQL转换工具根据源端到目标端数据库的语法转换规则对此DDL进行语法转换，以适配目标数据库的语法特性。如果转换成功，会尝试在目标数据库中执行转换后的SQL，执行成功则说明此对象为自动兼容，可以进行迁移。

## CREATE TABLE

|  序号| MySQL语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------- |
| [1](#1) | 临时表TEMPORARY关键字                                        | 转为全局临时表                                               | 同左                   |
| [2](#2) | create_definition: {<br>col_name column_definition<br>&#124; {INDEX &#124; KEY} [index_name] [index_type] (key_part,...)<br>[index_option] ... （表内索引）<br> &#124; {FULLTEXT &#124; SPATIAL} [INDEX &#124; KEY] [index_name] (key_part,...)<br>[index_option] ...<br> &#124; [CONSTRAINT [symbol]] PRIMARY KEY  （表内主键）<br>[index_type] (key_part,...)<br>[index_option] ...<br> &#124; [CONSTRAINT [symbol]] UNIQUE [INDEX                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             &#124; KEY] （索引定义）<br>[index_name] [index_type] (key_part,...)<br>[index_option] ...<br> &#124; [CONSTRAINT [symbol]] FOREIGN KEY （外键引用定义）<br>[index_name] (col_name,...)<br>reference_definition  （外键引用定义）<br> &#124; check_constraint_definition<br>} | 只保留col_name column_definition                             | 同左                   |
| [3](#3) | column_definition: {<br>data_type [NOT NULL &#124; NULL] [DEFAULT {literal &#124; (expr)} ]<br>[VISIBLE &#124; INVISIBLE]<br>[AUTO_INCREMENT] [UNIQUE [KEY]] [[PRIMARY] KEY]<br>[COMMENT 'string'] （列注释信息）<br>[COLLATE collation_name]<br>[COLUMN_FORMAT {FIXED &#124; DYNAMIC &#124; DEFAULT}]<br>[ENGINE_ATTRIBUTE [=] 'string']<br>[SECONDARY_ENGINE_ATTRIBUTE [=] 'string']<br>[STORAGE {DISK &#124; MEMORY}]<br>[reference_definition]<br>[check_constraint_definition]<br>&#124; data_type<br>[COLLATE collation_name]<br>[GENERATED ALWAYS] AS (expr)<br>[VIRTUAL &#124; STORED] [NOT NULL &#124; NULL]<br>[VISIBLE &#124; INVISIBLE]<br>[UNIQUE [KEY]] [[PRIMARY] KEY]<br>[COMMENT 'string']<br>[reference_definition]<br>[check_constraint_definition]<br>} | 只保留data_type，和DEFAULT 。<br/>AUTO_INCREMENT和comment语法会转化为单独的语句，分别跟随在表定义前和后。<br/>注：仅部分数据类型可以正常转出。 | 同左                   |
| [4](#4) | table_option: {<br>AUTOEXTEND_SIZE [=] value<br>&#124; AUTO_INCREMENT [=] value<br>&#124; AVG_ROW_LENGTH [=] value<br>&#124; [DEFAULT] CHARACTER SET [=] charset_name<br>&#124; CHECKSUM [=] {0 &#124; 1}<br>&#124; [DEFAULT] COLLATE [=] collation_name<br>&#124; COMMENT [=] 'string'<br>&#124; COMPRESSION [=] {'ZLIB' &#124; 'LZ4' &#124; 'NONE'}<br>&#124; CONNECTION [=] 'connect_string'<br>&#124; {DATA &#124; INDEX} DIRECTORY [=] 'absolute path to directory'<br>&#124; DELAY_KEY_WRITE [=] {0 &#124; 1}<br>&#124; ENCRYPTION [=] {'Y' &#124; 'N'}<br>&#124; ENGINE [=] engine_name<br>&#124; ENGINE_ATTRIBUTE [=] 'string'<br>&#124; INSERT_METHOD [=] { NO &#124; FIRST &#124; LAST }<br>&#124; KEY_BLOCK_SIZE [=] value<br>&#124; MAX_ROWS [=] value<br>&#124; MIN_ROWS [=] value<br>&#124; PACK_KEYS [=] {0 &#124; 1 &#124; DEFAULT}<br>&#124; PASSWORD [=] 'string'<br>&#124; ROW_FORMAT [=] {DEFAULT &#124; DYNAMIC &#124; FIXED &#124; COMPRESSED &#124; REDUNDANT &#124; COMPACT}<br>&#124; START TRANSACTION <br>&#124; SECONDARY_ENGINE_ATTRIBUTE [=] 'string'<br>&#124; STATS_AUTO_RECALC [=] {DEFAULT &#124; 0 &#124; 1}<br>&#124; STATS_PERSISTENT [=] {DEFAULT &#124; 0 &#124; 1}<br>&#124; STATS_SAMPLE_PAGES [=] value<br>&#124; tablespace_option<br>&#124; UNION [=] (tbl_name[,tbl_name]...)<br>} | AUTO_INCREMENT [=] value会换为表前定义序列的start with的起始值。<br><br>COMMENT [=] 'string'会转为表后的单独comment语句<br><br><br><br>tablespace_option初始获取可能会带有注释形式，移除注释（YMP处理），tablespace正常转换出来（保留名字）。 | 同左                   |
| [5](#5) | tablespace_option:<br>TABLESPACE tablespace_name [STORAGE DISK]<br>&#124; [TABLESPACE tablespace_name] STORAGE MEMORY | 只保留表空间名称，删除[STORAGE DISK]<br><br>如果表空间名称为innodb_file_per_table，则表示为默认表空间，因此转换时会丢弃表空间名称信息 | 同左                   |
| [6](#6) | partition_definition:<br>PARTITION partition_name  分区定义语法<br>    [VALUES<br>    {LESS THAN {(expr &#124; value_list) &#124; MAXVALUE}<br>      &#124;<br>      IN (value_list)}]<br>      [[STORAGE] ENGINE [=] engine_name]<br>      [COMMENT [=] 'string' ]<br>      [DATA DIRECTORY [=] 'data_dir']<br>      [INDEX DIRECTORY [=] 'index_dir']<br>      [MAX_ROWS [=] max_number_of_rows]<br>      [MIN_ROWS [=] min_number_of_rows]<br>      [TABLESPACE [=] tablespace_name]<br>      [(subpartition_definition [, subpartition_definition] ...)] | 只保留YashanDB支持的分区定义信息,<br><br>[[STORAGE] ENGINE [=] engine_name]<br>[COMMENT [=] 'string' ]<br>[DATA DIRECTORY [=] 'data_dir']<br>[INDEX DIRECTORY [=] 'index_dir']<br>[MAX_ROWS [=] max_number_of_rows]<br>[MIN_ROWS [=] min_number_of_rows]<br><br>都会被删除<br><br>分区信息初始获取可能会带有注释形式，移除注释（YMP处理） | 同左                   |
| [7](#7) | subpartition_definition: 子分区定义语法<br><br>SUBPARTITION logical_name<br><br>[[STORAGE] ENGINE [=] engine_name]<br><br>[COMMENT [=] 'string' ]<br><br>[DATA DIRECTORY [=] 'data_dir']<br><br>[INDEX DIRECTORY [=] 'index_dir']<br><br>[MAX_ROWS [=] max_number_of_rows]<br><br>[MIN_ROWS [=] min_number_of_rows]<br><br>[TABLESPACE [=] tablespace_name] | 22.2无该语法 ，转换时删除                                    | 23.1保留子分区转换输出 |
| [8](#8) | ON UPDATE CURRENT_TIMESTAMP(n)                               | 转换成触发器                                                 | 同左                   |

## CREATE VIEW

|  序号| MySQL语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
| --------- | ------------------------------------------------------------ | ------------------------------------------- | -------------------- |
| [9](#9)   | CREATE<br>[OR REPLACE]<br>[SQL SECURITY { DEFINER &#124; INVOKER }]<br>VIEW view_name [(column_list)]<br>AS | [SQL SECURITY { DEFINER &#124; INVOKER }] 删除 | 同左                 |
| [10](#10) | [WITH [CASCADED &#124; LOCAL] CHECK OPTION]                    | 全部删除                                    | 同左                 |
| 11        | SELECT 子查询                                                | 保持语义输出                                | 同左                 |

## CREATE INDEX

|  序号| MySQL语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
| --------- | ------------------------------------------------------------ | -------------------------- | -------------------- |
| [12](#12) | CREATE [UNIQUE &#124; FULLTEXT &#124; SPATIAL] INDEX index_name     | 仅支持UNIQUE类型和普通类型 | 同左                 |
| [13](#13) | index_option: {<br>KEY_BLOCK_SIZE [=] value<br>&#124; index_type<br>&#124; WITH PARSER parser_name<br>&#124; COMMENT 'string'<br>&#124; {VISIBLE &#124; INVISIBLE}<br>&#124; ENGINE_ATTRIBUTE [=] 'string'<br>&#124; SECONDARY_ENGINE_ATTRIBUTE [=] 'string'<br>}<br><br>index_type:<br>USING {BTREE &#124; HASH}<br><br>algorithm_option:<br>ALGORITHM [=] {DEFAULT &#124; INPLACE &#124; COPY}<br><br>lock_option:<br>LOCK [=] {DEFAULT &#124; NONE &#124; SHARED &#124; EXCLUSIVE} | 全部删除                   | 同左                 |

## ALTER TABLE ADD CONSTRAINT / MODIFY NOT NULL

|  序号| MySQL语法/关键字| YashanDB22.2转换规则| YashanDB23.1转换规则|
| --------- | ------------------------------------------------------------ | ----------------------------- | -------------------- |
| [14](#14) | [CONSTRAINT [symbol]] PRIMARY KEY<br><br>[index_type] (key_part,...)<br><br>[index_option] ... | 删除index_type 和index_option | 同左                 |
| [15](#15) | ADD [CONSTRAINT [symbol]] UNIQUE [INDEX &#124; KEY]<br>[index_name] [index_type] (key_part,...)<br>[index_option] ... | 删除index_type 和index_option | 同左                 |
| 16        | ADD [CONSTRAINT [symbol]] FOREIGN KEY<br>[index_name] (col_name,...)<br>reference_definition | 一致                          | 同左                 |
| [17](#17) | ADD [CONSTRAINT [symbol]] CHECK (expr) [[NOT] ENFORCED]  | 删除[[NOT] ENFORCED]        | 同左                 |
| [18](#18) | MODIFY COLUMN_NAME DATATYPE NOT NULL                         | 删除DATATYPE                  | 同左                 |

## CREATE EVENT

MySQL的Event对应对象为YashanDB的JOB，通过查询information_schema.EVENTS视图，将所需要的关键信息进行组装拼接成YashanDB的JOB的创建语句。

所使用到information_schema.EVENTS的关键字段和含义如下表所示。

| EVENT_SCHEMA  | EVENT_NAME | EVENT_DEFINITION | EVENT_TYPE      | EXECUTE_AT | INTERVAL_VALUE | INTERVAL_FIELD | STARTS         | ENDS           | STATUS | ON_COMPLETION | EVENT_COMMENT |
|---------------|------------|------------------|-----------------|------------|----------------|----------------|----------------|----------------|--------|---------------|---------------|
| Event所属Schema | Event名称    | Event要执行的SQL语句   | event类型 （循环/单词） | 	执行时间      | 执行间隔值          | 执行间隔时间的单位      | 该event开始起作用的时间 | 该event结束起作用的时间 | 对象状态   | 完成后是否被删除      | 对象的COMMENT信息  |  

对上面的关键信息进行拼接，就可以获取对应的YashanDB的JOB创建语句，YashanDB的JOB创建语句样式如下：

```sql
EXEC DBMS_SCHEDULER.CREATE_JOB (
    job_name IN VARCHAR,   -- 对应字段  EVENT_NAME
    job_type IN VARCHAR,    -- 对应字段  EVENT_TYPE
    job_action IN VARCHAR , -- 对应字段   begin  EVENT_DEFINITION end ;
    number_of_arguments IN INTEGER DEFAULT 0,   -- 无对应字段  保留字段，使用缺省值 0。
    start_date IN TIMESTAMP DEFAULT NULL,   -- 对应字段    STARTS /  EXECUTE_AT
    repeat_interval IN VARCHAR DEFAULT NULL,    -- 对应字段 INTERVAL_VALUE INTERVAL_FIELD
    end_date IN TIMESTAMP DEFAULT NULL, -- 对应字段   ENDS
    job_class IN VARCHAR DEFAULT 'DEFAULT_JOB_CLASS',   -- 无对应字段  保留字段，使用缺省值   'DEFAULT_JOB_CLASS'。
    enabled IN BOOLEAN DEFAULT FALSE,   -- 对应字段  STATUS
    auto_drop IN BOOLEAN DEFAULT TRUE,  -- 对应字段   ON_COMPLETION
    comments IN VARCHAR DEFAULT NULL);  -- 对应字段  EVENT_COMMENT
```

示例[19](#19)展示了一个MySQL的Event转换为YashanDB的JOB的样例。

## PLSQL对象处理

|  对象类型| 规则| MySQL语法| YashanDB预期|
|-----------|-------------|-------------------------------------------------------------|-------------------------------------------|
| 触发器       |             | CREATE [DEFINER = user] TRIGGER [IF NOT EXISTS] sp_name   | CREATE or replace  TRIGGER  "SP_NAME"     |
| 函数        |             | CREATE [DEFINER = user] FUNCTION [IF NOT EXISTS] sp_name  | CREATE or replace  FUNCTION  "SP_NAME"    |
| 存储过程      |             | CREATE [DEFINER = user] PROCEDURE [IF NOT EXISTS] sp_name | CREATE or replace PROCEDURE  "SP_NAME"    |
| 对象名左右两边 ` | 替换为" 且内部转大写 | ```sql create table `xxx` (`id` int );```                   | ```sql create table "XXX" ("ID" int );``` |

## 全部示例

<span id="1" name="1" class="yaslink"></span>

### 示例1

```sql
 -- MySQL
 CREATE TEMPORARY TABLE x(id INT)
 
 -- YashanDB22.2预期结果
 CREATE GLOBAL TEMPORARY TABLE "X" ("ID" INT);
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="2" name="2" class="yaslink"></span>

### 示例2

```sql
 -- MySQL
 CREATE TABLE MYL.TEST4 (col1 INT REFERENCES MYL.T2(col1) ON DELETE SET null, col2 INT DEFAULT 1 CHECK(col2 < 30));
 
 -- YashanDB22.2预期结果
 CREATE TABLE "MYL"."TEST4" (
    "COL1" INT,
    "COL2" INT DEFAULT 1
);
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="3" name="3" class="yaslink"></span>

### 示例3

```sql
 -- MySQL
CREATE TABLE x(id INT auto_increment DEFAULT 'xxx' COMMENT 'xxx');

 -- YashanDB22.2预期结果
 CREATE SEQUENCE "X_ID_SEQ" INCREMENT BY 1 START WITH 1;
CREATE TABLE "X" (
    "ID" INT DEFAULT 'xxx' DEFAULT "X_ID_SEQ".NEXTVAL
);
 
COMMENT ON COLUMN "X"."ID" IS 'xxx';
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="4" name="4" class="yaslink"></span>

### 示例4

```sql
 -- MySQL
CREATE TABLE x(id INT auto_increment DEFAULT 'xxx' COMMENT 'xxx') COMMENT 'xxx' auto_increment = 1231 TABLESPACE xxx STORAGE DISK;

 -- YashanDB22.2预期结果
 CREATE SEQUENCE "X_ID_SEQ" INCREMENT BY 1 START WITH 1231;
CREATE TABLE x (
    "ID" INT DEFAULT 'xxx' DEFAULT "X_ID_SEQ".NEXTVAL
) TABLESPACE "XXXDISK";
 
COMMENT ON COLUMN "X"."ID" IS 'xxx';
COMMENT ON TABLE "X" IS 'xxx';
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="5" name="5" class="yaslink"></span>

### 示例5

```sql
 -- MySQL
 CREATE TABLE x(id INT auto_increment DEFAULT 'xxx' COMMENT 'xxx') COMMENT 'xxx' auto_increment = 1231 TABLESPACE xxx STORAGE DISK;
 
 -- YashanDB22.2预期结果
 CREATE SEQUENCE "X_ID_SEQ" INCREMENT BY 1 START WITH 1231;
CREATE TABLE "x" (
    "ID" INT DEFAULT 'xxx' DEFAULT "X_ID_SEQ".NEXTVAL
) TABLESPACE "XXXDISK";
 
COMMENT ON COLUMN "X"."ID" IS 'xxx';
COMMENT ON TABLE "X" IS 'xxx';
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="6" name="6" class="yaslink"></span>

### 示例6

```sql
 -- MySQL 	
CREATE TABLE th (id INT, name VARCHAR(30), adate DATE)
PARTITION BY LIST(YEAR(adate))
(
  PARTITION p1999 VALUES IN (1995, 1999, 2003)
    DATA DIRECTORY = '/var/appdata/95/data'
    INDEX DIRECTORY = '/var/appdata/95/idx',
  PARTITION p2000 VALUES IN (1996, 2000, 2004)
    DATA DIRECTORY = '/var/appdata/96/data'
    INDEX DIRECTORY = '/var/appdata/96/idx' TABLESPACE  xxx,
  PARTITION p2001 VALUES IN (1997, 2001, 2005)
    DATA DIRECTORY = '/var/appdata/97/data'
    INDEX DIRECTORY = '/var/appdata/97/idx',
  PARTITION p2002 VALUES IN (1998, 2002, 2006)
    DATA DIRECTORY = '/var/appdata/98/data'
    INDEX DIRECTORY = '/var/appdata/98/idx'
);
 -- YashanDB22.2预期结果
 CREATE TABLE "TH" (
    "ID" INT,
    "NAME" VARCHAR(30 CHAR),
    "ADATE" DATE
)
PARTITION BY LIST (YEAR("ADATE")) ( PARTITION "P1999" VALUES ((1995), (1999), (2003)) , PARTITION "P2000" VALUES ((1996), (2000), (2004))
    TABLESPACE "XXX", PARTITION "P2001" VALUES ((1997), (2001), (2005)) , PARTITION "P2002" VALUES ((1998), (2002), (2006))
);
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="7" name="7" class="yaslink"></span>

### 示例7

```sql
 -- MySQL
 CREATE TABLE ts (id INT, purchased DATE)
    PARTITION BY RANGE( YEAR(purchased) )
    SUBPARTITION BY HASH( TO_DAYS(purchased) )
    SUBPARTITIONS 2 (
        PARTITION p0 VALUES LESS THAN (1990),
        PARTITION p1 VALUES LESS THAN (2000),
        PARTITION p2 VALUES LESS THAN MAXVALUE
    );
 -- YashanDB22.2预期结果
 CREATE TABLE "TS" (
    "ID" INT,
    "PURCHASED" DATE
)
PARTITION BY RANGE (YEAR("PURCHASED"))
 ( PARTITION "P0" VALUES LESS THAN (1990) , PARTITION "P1" VALUES LESS THAN (2000) , PARTITION "P2" VALUES LESS THAN (MAXVALUE) );
 -- YashanDB23.1预期结果
 CREATE TABLE "TS" (
    "ID" INT,
    "PURCHASED" DATE
)
PARTITION BY RANGE (YEAR("PURCHASED"))
SUBPARTITION BY HASH(TO_DAYS("PURCHASED")) SUBPARTITIONS  2
 ( PARTITION "P0" VALUES LESS THAN (1990) , PARTITION "P0" VALUES LESS THAN (2000) , PARTITION "P2" VALUES LESS THAN (MAXVALUE) );
 -- 同上
```

<span id="8" name="8" class="yaslink"></span>

### 示例8

```sql
 -- MySQL
CREATE TABLE `test_timestamp`(
    id INT , 
    t1 TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP(3) ON UPDATE CURRENT_TIMESTAMP(3))
 -- YashanDB22.2预期结果
CREATE TABLE "TEST_TIMESTAMP" (
	"ID" INT,
	"T1" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP()
)
;
CREATE TRIGGER "TEST_TIMESTAMP_T1_TRI"
BEFORE UPDATE ON "TEST_TIMESTAMP"
FOR EACH ROW
 BEGIN
   IF NOT UPDATING('T1')  THEN 
   		:NEW."T1" := CURRENT_TIMESTAMP();
   END IF;
END;/
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="9" name="9" class="yaslink"></span>

### 示例9

```sql
 -- MySQL
 CREATE OR replace  VIEW v3 (idalias ) AS SELECT id FROM v1 WHERE a > 0
 -- YashanDB22.2预期结果
 CREATE OR REPLACE VIEW "V3"
AS
SELECT "ID"
FROM "V1"
WHERE "A" > 0;
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="10" name="10" class="yaslink"></span>

### 示例10

```sql
 -- MySQL
 CREATE OR replace  VIEW v3 (idalias ) AS SELECT id FROM v1 WHERE a > 0
WITH CASCADED CHECK OPTION;
 -- YashanDB22.2预期结果
 CREATE OR replace  VIEW "V3" ("IDALIAS" ) AS SELECT "ID" FROM "V1" WHERE "A" > 0

 -- YashanDB23.1预期结果
 -- 同上
```

<span id="12" name="12" class="yaslink"></span>

### 示例12

```sql
 -- MySQL
 /*The 1 SQL statement*/
CREATE UNIQUE INDEX xxx ON x(x);
 
/*The 2 SQL statement*/
CREATE FULLTEXT INDEX xxx ON x(x);
 
/*The 3 SQL statement*/
CREATE INDEX xxx ON x(x);

 -- YashanDB22.2预期结果
 /*The 1 SQL statement*/
CREATE UNIQUE INDEX "XXX" ON "X" ("X");
 
/*The 2 SQL statement*/
CREATE  INDEX "XXX" ON "X" ("X");
 
/*The 3 SQL statement*/
CREATE  INDEX "XXX" ON "X" ("X");
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="13" name="13" class="yaslink"></span>

### 示例13

```sql
 -- MySQL
 /*The 1 SQL statement*/
CREATE INDEX id_index ON lookup (id) USING BTREE;
 
/*The 2 SQL statement*/
CREATE INDEX id_index ON t1 (id) COMMENT 'MERGE_THRESHOLD=40';

 -- YashanDB22.2预期结果
 /*The 1 SQL statement*/
CREATE INDEX "ID_INDEX" ON "LOOKUP" ("ID");
 
/*The 2 SQL statement*/
CREATE INDEX "ID_INDEX" ON "T1" ("ID");
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="14" name="14" class="yaslink"></span>

### 示例14

```sql
 -- MySQL
 ALTER TABLE xx ADD PRIMARY USING BTREE (id);
 -- YashanDB22.2预期结果
 ALTER TABLE "XX" ADD PRIMARY KEY ("ID");
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="15" name="15" class="yaslink"></span>

### 示例15

```sql
 -- MySQL
 ALTER TABLE student ADD CONSTRAINT  xx UNIQUE KEY USING BTREE(id)  ;
 -- YashanDB22.2预期结果
 ALTER TABLE "STUDENT" ADD CONSTRAINT "XX" UNIQUE ("ID");
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="17" name="17" class="yaslink"></span>

### 示例17

```sql
 -- MySQL
ALTER TABLE student ADD CHECK (id>0) ENFORCED ;
 -- YashanDB22.2预期结果
 ALTER TABLE "STUDENT" ADD CHECK ("ID">0) ;
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="18" name="18" class="yaslink"></span>

### 示例18

```sql
 -- MySQL
 ALTER TABLE X MODIFY ID INT NOT NULL;
 -- YashanDB22.2预期结果
 ALTER TABLE "X" MODIFY "ID"  NOT NULL;
 -- YashanDB23.1预期结果
 -- 同上
```

<span id="19" name="19" class="yaslink"></span>

### 示例19

```sql
 -- MySQL
 CREATE DEFINER=`root`@`%` EVENT `myevent` 
    ON SCHEDULE AT '2024-01-09 06:14:00' 
    ON COMPLETION PRESERVE DISABLE ON SLAVE 
    DO 
        UPDATE mytable SET mycol = mycol + 1;
    ;   
 -- YashanDB22.2预期结果
 EXEC DBMS_SCHEDULER.CREATE_JOB('LSP.myevent',
    'PLSQL_BLOCK', 
    ' BEGIN UPDATE mytable SET mycol = mycol + 1 ; END ;' ,
    0,
    TO_TIMESTAMP('2024-01-09 06:14:00', 'YYYY-MM-DD HH24:MI:SS'),
    null,
    null,
    'DEFAULT_JOB_CLASS',
    false,
    false,
    null);
 -- YashanDB23.1预期结果
 -- 同上
```
