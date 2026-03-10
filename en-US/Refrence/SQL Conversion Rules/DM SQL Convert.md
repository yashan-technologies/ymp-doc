This document is based on the existing syntax of DM8 and has been structured for conversion according to SQL transformation rules.

## Overview

During the task evaluation phase of YMP, a compatibility assessment of objects in the source database (such as tables, views, constraints, indexes, etc.) will be conducted to ensure that source database objects can be successfully migrated to the target database. In the evaluation phase, attempts will be made to retrieve the DDL of the source database objects and execute it in the target database. If execution is successful, it indicates that this object is natively compatible and does not require special handling. Otherwise, the SQL transformation tool will perform a syntax transformation on this DDL based on the syntax conversion rules from the source to the target database, to adapt to the syntax features of the target database. If the conversion is successful, attempts will be made to execute the transformed SQL in the target database. If execution is successful, it indicates that this object is automatically compatible and can be migrated. 

## CREATE TABLE

|Serial No. |DM Syntax/Keyword |YashanDB22.2 Transformation Rules |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [1](#1)    | \<AUTO_INCREMENT clause> IDENTITY [(\<seed>,\<increment>)]      | Converted to independent sequence object                  |
| 2          | STORAGE clause                                                | Deleted                                                |
| 3          | Constraints and NOT NULL information within the table         | Converted to independent constraint object               |
| 4          | PARTITION clause:<br> PARTITION BY \<PARTITION item><br>PARTITION item:<br>RANGE (\<column name>{,\<column name>})[INTERVAL (\<interval expression>)] (\<RANGE partition item> {,\<RANGE partition item>})<br> | Only retain partition definition information supported by YashanDB |
| 5          | RANGE partition item:<br>PARTITION \<partition name> VALUES LESS THAN <br>"(< \<constant expression>| MAXVALUE >{,< \<constant expression>|\<MAXVALUE >})"<br>[\<tablespace clause>]<br>[\<STORAGE clause>] | Only retain constant expressions supported by YashanDB<br/>Tablespace clause and STORAGE clause will be deleted |
| 6          | HASH partition item:<br>PARTITION \<partition name><br>[\<tablespace clause>]<br>[\<STORAGE clause>] | Tablespace clause and STORAGE clause will be deleted      |
| 7          | LIST partition item:<br>PARTITION \<partition name> VALUES <br>"(DEFAULT|\<expression>,{\<expression>})"<br>[\<tablespace clause>]<br>[\<STORAGE clause>] | Only retain expressions supported by YashanDB<br/>Tablespace clause and STORAGE clause will be deleted |
| 8          | STORAGE item:<br>INITIAL \<initial cluster number>            | Deleted                                                |
| 9          | Tablespace clause:<br>TABLESPACE \<tablespace name>          | Deleted                                                |

## CREATE INDEX

|Serial No. |DM Syntax/Keyword |YashanDB22.2 Transformation Rules |
|-----------|-----------------------------------------------------|-------------------------------------------|
| [10](#10)  | [CLUSTER|NOT PARTIAL][UNIQUE | BITMAP | SPATIAL]       | Only supports UNIQUE type and normal type; [CLUSTER|NOT PARTIAL] is excluded |
| 11         | STORAGE clause                                            | Deleted                                                |

## ALTER TABLE ADD CONSTRAINT / SET NOT NULL

|Serial No. |DM Syntax/Keyword |YashanDB22.2 Transformation Rules |
|-----------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------|
| [12](#12) | ALTER COLUMN_NAME SET NOT NULL                                                                                            | MODIFY COLUMN_NAME NOT NULL |
| [13](#13)  | CLUSTER and other keywords                                                                                             | Deleted                                        |

## CREATE VIEW

|Serial No. |DM Syntax/Keyword |YashanDB22.2 Transformation Rules |
|----------|-----------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| [14](#14)  | CREATE<br>[OR REPLACE] <br>VIEW <br>[\<schema name>.]\<view name><br>[(\<column name> {,\<column name>})] AS \<query statement> | Retain OR REPLACE                   |
| 15         | Query statement:<br>\<table query><br>|\<table join>                     | Only retain query syntax supported by YashanDB |

## All Examples

<span id="1" name="1" class="yaslink"></span>

### Example 1

```sql
 -- DM
 CREATE TABLE "TEST_1"
("ID" INT AUTO_INCREMENT NOT NULL,
NOT CLUSTER PRIMARY KEY("ID")) 
STORAGE(ON "MAIN", CLUSTERBTR) AUTO_INCREMENT = 1;

CREATE TABLE "T33"
("ID" INT IDENTITY(100, 1) NOT NULL,
"ID2" INT NOT NULL) STORAGE(ON "MAIN", CLUSTERBTR) ;

 -- YashanDB22.2 expected result
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

### Example 2

```sql
 -- DM
CREATE OR REPLACE CLUSTER  INDEX "CLINDEX" ON "XIASS"."T1"("ID" ASC) STORAGE(ON "MAIN", CLUSTERBTR) ;
 -- YashanDB22.2 expected result
CREATE INDEX "CLINDEX" ON "XIASS"."T1" ("ID" ASC); 
```

<span id="12" name="12" class="yaslink"></span>

### Example 3

```sql
 -- DM
ALTER TABLE "USER_1"."T33" ALTER COLUMN  "ID" SET NOT NULL;
 -- YashanDB22.2 expected result
ALTER TABLE "USER_1"."T33"  MODIFY "ID" NOT NULL; 
```

<span id="13" name="13" class="yaslink"></span>

### Example 4

```sql
 -- DM
ALTER TABLE "USER_1"."PK"  ADD CONSTRAINT "CONS134219820" NOT CLUSTER PRIMARY KEY("ID");
 -- YashanDB22.2 expected result
ALTER TABLE "USER_1"."PK" ADD CONSTRAINT "CONS134219820" PRIMARY KEY ("ID");
```

<span id="14" name="14" class="yaslink"></span>

### Example 5

```sql
 -- DM
CREATE  OR REPLACE  VIEW TEST1926.VIEW4 AS  SELECT TEST1926.VIEW_TABLE1.COL0 FROM TEST1926.VIEW_TABLE1;
 -- YashanDB22.2 expected result
CREATE  OR REPLACE  VIEW TEST1926.VIEW4 AS  SELECT TEST1926.VIEW_TABLE1.COL0 FROM TEST1926.VIEW_TABLE1;
```
