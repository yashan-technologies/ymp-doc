本文档基于DM8已有语法，根据SQL转换对以下语法进行了规则化的转换。

## 概述

在YMP的任务评估阶段，会对源数据库中的对象（表、视图、约束、索引等）进行兼容性评估，以确保源端数据库对象可以成功迁移到目标数据库。在评估阶段，会尝试获取源端数据库对象的DDL并在目标数据库执行。如果执行成功，则说明此对象为原生兼容，无需进行特殊处理。否则，会使用SQL转换工具根据源端到目标端数据库的语法转换规则对此DDL进行语法转换，以适配目标数据库的语法特性。如果转换成功，会尝试在目标数据库中执行转换后的SQL，执行成功则说明此对象为自动兼容，可以进行迁移。

## CREATE TABLE

|  序号| DM语法/关键字| YashanDB22.2转换规则|
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [1](#1) | \<AUTO_INCREMENT子句>  IDENTITY [(\<种子>,\<增量>)]             | 转为独立序列对象                                             |
| 2       | STORAGE子句                                                  | 删除                                                         |
| 3       | 表内约束和非空信息                                           | 转为独立约束对象                                             |
| 4       | PARTITION子句：<br> PARTITION BY \<PARTITION项><br>PARTITION项：<br>RANGE （\<列名>{，\<列名>}）[INTERVAL （\<间隔表达式>）] （\<RANGE分区项> {，\<RANGE分区项>}）<br>&#124;HASH （\<列名>{，\<列名>}） PARTITIONS \<分区数><br>&#124;HASH（\<列名>{，\<列名>}）（\<HASH分区项> {，\<HASH分区项>}）<br>&#124;LIST（\<列名>（\<LIST分区项> {，\<LIST分区项>}）） | 只保留YashanDB支持的分区定义信息                             |
| 5       | RANGE分区项：<br>PARTITION \<分区名> VALUES LESS THAN <br>"(< \<常量表达式>&#124; MAXVALUE >{,< \<常量表达式>&#124;\<MAXVALUE >})"<br>[\<表空间子句>]<br>[\<STORAGE子句>] | 只保留YashanDB支持的常量表达式<br/>表空间子句 与 STORAGE子句会被删除 |
| 6       | HASH分区项：<br>PARTITION \<分区名> <br>[\<表空间子句>]<br>[\<STORAGE子句> | 表空间子句 与 STORAGE子句会被删除                            |
| 7       | LIST分区项：<br>PARTITION \<分区名> VALUES <br>"(DEFAULT&#124;\<表达式>，{\<表达式>}>) <br>[\<表空间子句>]"<br>[\<STORAGE子句>] | 只保留YashanDB支持的表达式<br/>表空间子句 与 STORAGE子句会被删除 |
| 8       | STORAGE项：<br>INITIAL \<初始簇数目>                          | 删除                                                         |
| 9       | 表空间子句：<br>TABLESPACE \<表空间名>                        | 删除                                                         |

## CREATE INDEX

|  序号| DM语法/关键字| YashanDB22.2转换规则|
|-----------|-----------------------------------------------------|-------------------------------------------|
| [10](#10) | [CLUSTER&#124;NOT PARTIAL][UNIQUE &#124; BITMAP &#124; SPATIAL] | 仅支持UNIQUE类型和普通类型，[CLUSTER&#124;NOT PARTIAL]剔除 | 
| 11        | STORAGE子句                                           | 删除                                        | 

## ALTER TABLE ADD CONSTRAINT / SET NOT NULL

|  序号| DM语法/关键字| YashanDB22.2转换规则|
|-----------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------|
| [12](#12) | ALTER COLUMN_NAME SET NOT NULL                                                                                            | MODIFY COLUMN_NAME NOT NULL |
| [13](#13) | CLUSTER等关键字                                                                                                               | 删除                          |

## CREATE VIEW

|  序号| DM语法/关键字| YashanDB22.2转换规则|
|----------|-----------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| [14](#14) | CREATE<br>[OR REPLACE] <br>VIEW <br>[\<模式名>.]\<视图名><br>[(\<列名> {,\<列名>})] AS \<查询说明> | 保留OR REPLACE       | 
| 15       | 查询说明：<br>\<表查询><br>&#124;>\<表连接> | 只保留YashanDB支持的查询语法 | 

## 全部示例

<span id="1" name="1" class="yaslink"></span>

### 示例1

```sql
 -- DM
 CREATE TABLE "TEST_1"
("ID" INT AUTO_INCREMENT NOT NULL,
NOT CLUSTER PRIMARY KEY("ID")) 
STORAGE(ON "MAIN", CLUSTERBTR) AUTO_INCREMENT = 1;

CREATE TABLE "T33"
("ID" INT IDENTITY(100, 1) NOT NULL,
"ID2" INT NOT NULL) STORAGE(ON "MAIN", CLUSTERBTR) ;

 -- YashanDB22.2预期结果
CREATE SEQUENCE TEST_1_ID_SEQ START WITH 1;
CREATE TABLE "TEST_1" (
	"ID" INT DEFAULT "TEST_1_ID_SEQ".NEXTVAL
);

CREATE SEQUENCE T33_ID_SEQ INCREMENT BY 100 START WITH 1
CREATE TABLE "T33" (
	"ID" INT DEFAULT "T33_ID_SEQ".NEXTVAL,
	"ID2" INT
);
```

<span id="10" name="10" class="yaslink"></span>

### 示例2

```sql
 -- DM
CREATE OR REPLACE CLUSTER  INDEX "CLINDEX" ON "XIASS"."T1"("ID" ASC) STORAGE(ON "MAIN", CLUSTERBTR) ;
 -- YashanDB22.2预期结果
CREATE INDEX "CLINDEX" ON "XIASS"."T1" ("ID" ASC); 
```

<span id="12" name="12" class="yaslink"></span>

### 示例3

```sql
 -- DM
ALTER TABLE "USER_1"."T33" ALTER COLUMN  "ID" SET NOT NULL;
 -- YashanDB22.2预期结果
ALTER TABLE "USER_1"."T33"  MODIFY "ID" NOT NULL; 
```

<span id="13" name="13" class="yaslink"></span>

### 示例4

```sql
 -- DM
ALTER TABLE "USER_1"."PK"  ADD CONSTRAINT "CONS134219820" NOT CLUSTER PRIMARY KEY("ID");
 -- YashanDB22.2预期结果
ALTER TABLE "USER_1"."PK" ADD CONSTRAINT "CONS134219820" PRIMARY KEY ("ID");
```

<span id="14" name="14" class="yaslink"></span>

### 示例5

```sql
 -- DM
CREATE  OR REPLACE  VIEW TEST1926.VIEW4 AS  SELECT TEST1926.VIEW_TABLE1.COL0 FROM TEST1926.VIEW_TABLE1;
 -- YashanDB22.2预期结果
CREATE  OR REPLACE  VIEW TEST1926.VIEW4 AS  SELECT TEST1926.VIEW_TABLE1.COL0 FROM TEST1926.VIEW_TABLE1;
```
