本文档基于DB2已有语法，根据SQL转换对以下语法进行了规则化的转换。

## 概述

在YMP的任务评估阶段，会对源数据库中的对象（表、视图、约束、索引等）进行兼容性评估，以确保源端数据库对象可以成功迁移到目标数据库。  
在评估阶段，会尝试获取源端数据库对象的DDL并在目标数据库执行。如果执行成功，则说明此对象为原生兼容，无需进行特殊处理。 
否则，会使用SQL转换工具根据源端到目标端数据库的语法转换规则对此DDL进行语法转换，以适配目标数据库的语法特性。  
如果转换成功，会尝试在目标数据库中执行转换后的SQL，执行成功则说明此对象为自动兼容，可以进行迁移。
由于DB2没有提供系统的获取对象DDL的方法，所以在评估阶段DDL由DB2LOOK工具获取，可能会有部分语法不支持。

## CREATE TABLE

|  序号| DB2语法/关键字| YashanDB23转换规则|
|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [1](#1) | lob-options：[ NOT ] LOGGED &#124; [ NOT ] COMPACT                                                                                                                                                                                                                                                        | 删除                                                       | 
| 2       | generated-clause：GENERATED ALWAYS AS IDENTITY [ START WITH ( numeric-constant &#124; 1 ) ] [ INCREMENT BY ( numeric-constant &#124; 1 ) ] [ NO MAXVALUE &#124; MAXVALUE numeric-constant ] [ NO MINVALUE &#124; MINVALUE numeric-constant ] [ [ NO ] CYCLE ] [ NO CACHE &#124; CACHE numeric-constant ] [ [ NO ] ORDER ] | 仅在YashanDB版本>=23.4.2时保留，其余版本转为序列                         | 
| 3       | 表内约束                                                                                                                                                                                                                                                                                                 | 转为独立约束对象                                                 | 
| 4       | ORGANIZE BY ROW &#124; COLUMN                                                                                                                                                                                                                                                                            | 删除                                                       | 
| 5       | COMPRESS NO &#124; YES [ ADAPTIVE &#124; STATIC ]                                                                                                                                                                                                                                                            | 转为COMPRESS                                               | 
| 6       | [ INDEX &#124; LONG ] IN tablespace-name                                                                                                                                                                                                                                                                 | 只保留IN tablespace-name并转为TABLESPACE tablespace-name       | 
| [7](#7) | partition-expression：column-name [ NULLS LAST &#124; NULLS FIRST ]                                                                                                                                                                                                                                       | 只保留column-name                                           | 
| 8       | partition-element                                                                                                                                                                                                                                                                                    | 只保留PARTITION partition-name及partition-tablespace-options | 
| 9       | boundary-spec： [ starting-clause ] ending-clause                                                                                                                                                                                                                                                     | 只保留ending-clause并转为VALUES LESS THEN                      | 
| 10      | partition-tablespace-options： [ INDEX &#124; LONG ] IN tablespace-name                                                                                                                                                                                                                                   | 只保留IN tablespace-name并转为TABLESPACE tablespace-name       | 

## CREATE SEQUENCE

|  序号| DB2语法/关键字| YashanDB23转换规则|
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| [11](#11) | AS INTEGER &#124; data_type             | 删除                    | 
| 12        | [ START WITH ( numeric-constant &#124; 1 ) ] [ INCREMENT BY ( numeric-constant &#124; 1 ) ] [ NO MAXVALUE &#124; MAXVALUE numeric-constant ] [ NO MINVALUE &#124; MINVALUE numeric-constant ] [ [ NO ] CYCLE ] [ NO CACHE &#124; CACHE numeric-constant ] [ [ NO ] ORDER ] | 全部保留 |

## 全部示例

<span id="1" name="1" class="yaslink"></span>

### 示例1

```sql
 -- DB2
CREATE TABLE TABLE1  (
		  TABLE_ID INTEGER NOT NULL GENERATED ALWAYS AS IDENTITY (  
		    START WITH +1  
		    INCREMENT BY +1  
		    MINVALUE +1  
		    MAXVALUE +2147483647  
		    NO CYCLE  
		    CACHE 20  
		    NO ORDER ) , 
		  TABLE_NAME VARCHAR(32 OCTETS) , 
		  DETAIL VARCHAR(100 OCTETS) , 
		  DATASOURCE_ID INTEGER , 
		  CREATE_TIME TIMESTAMP , 
		  UPDATE_TIME TIMESTAMP )   
		 IN TBS_SYS_DATA INDEX IN TBS_SYS_IDX  
		 ORGANIZE BY ROW;
 -- YashanDB23预期结果
CREATE TABLE TABLE1 (
	TABLE_ID INT DEFAULT TABLE1_TABLE_ID_SEQ.NEXTVAL  NOT NULL,
	TABLE_NAME VARCHAR(32),
	DETAIL VARCHAR(100),
	DATASOURCE_ID INT,
	CREATE_TIME TIMESTAMP,
	UPDATE_TIME TIMESTAMP
)
TABLESPACE TBS_SYS_DATA;
```

<span id="7" name="7" class="yaslink"></span>

### 示例2

```sql
 -- DB2
CREATE TABLE TABLE3  (
		  JRXKZH VARCHAR(30 OCTETS) , 
		  NBJGH VARCHAR(30 OCTETS) , 
		  YHJGMC VARCHAR(450 OCTETS) , 
		  GYH VARCHAR(30 OCTETS) , 
		  GH VARCHAR(70 OCTETS) , 
		  GYLX VARCHAR(30 OCTETS) , 
		  SFSTGY VARCHAR(3 OCTETS) , 
		  GWBH VARCHAR(60 OCTETS) , 
		  GYQXJB VARCHAR(30 OCTETS) , 
		  SGRQ VARCHAR(8 OCTETS) , 
		  GYZT VARCHAR(30 OCTETS) , 
		  BBZ VARCHAR(600 OCTETS) , 
		  CJRQ CHAR(8 OCTETS) , 
		  GSFZJG VARCHAR(200 OCTETS) )   
		 COMPRESS YES ADAPTIVE  
		 INDEX IN TBS_BUS_IDX NOT LOGGED INITIALLY PARTITION BY RANGE(CJRQ) 
		 (PART 202001 STARTING('20200101') ENDING('20200131') IN TBS_BUS_DATA) 
		 ORGANIZE BY ROW;
-- YashanDB23预期结果
CREATE TABLE TABLE3 (
	JRXKZH VARCHAR(30),
	NBJGH VARCHAR(30),
	YHJGMC VARCHAR(450),
	GYH VARCHAR(30),
	GH VARCHAR(70),
	GYLX VARCHAR(30),
	SFSTGY VARCHAR(3),
	GWBH VARCHAR(60),
	GYQXJB VARCHAR(30),
	SGRQ VARCHAR(8),
	GYZT VARCHAR(30),
	BBZ VARCHAR(600),
	CJRQ CHAR(8),
	GSFZJG VARCHAR(200)
)
PARTITION BY RANGE (CJRQ)
 ( PARTITION 202001 VALUES LESS THAN ('20200131') 
	TABLESPACE TBS_BUS_DATA)
COMPRESS;

```

<span id="11" name="11" class="yaslink"></span>

### 示例3

```sql
 -- DB2
CREATE SEQUENCE SEQ1 AS INTEGER
	MINVALUE 1 MAXVALUE 2147483647
	START WITH 1 INCREMENT BY 1
	CACHE 20 NO CYCLE NO ORDER;
-- YashanDB23预期结果
CREATE SEQUENCE SEQ1 INCREMENT BY 1 START WITH 1 MAXVALUE 2147483647 MINVALUE 1 CACHE 20 NOCYCLE  NOORDER;

```
