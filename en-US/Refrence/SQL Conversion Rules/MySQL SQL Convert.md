This document is based on the existing syntax of MySQL 8.0 and has made standardized transformations of the following syntax according to SQL conversion.

## Overview

During the task evaluation phase of YMP, a compatibility assessment of objects (tables, views, constraints, indexes, etc.) in the source database is conducted to ensure that the source database objects can be successfully migrated to the target database. In the evaluation phase, an attempt is made to obtain the DDL of the source database objects and execute it in the target database. If the execution is successful, this indicates that the object is natively compatible and requires no special handling. Otherwise, the SQL conversion tool will use the syntax conversion rules from the source database to the target database to transform the DDL to fit the syntax features of the target database. If the conversion is successful, an attempt will be made to execute the converted SQL in the target database; successful execution indicates that this object is automatically compatible and can be migrated.

## CREATE TABLE

|No. |MySQL Syntax/Keyword |YashanDB 22.2 Transformation Rule |YashanDB 23.1 Transformation Rule |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------- |
| [1](#1)  | TEMPORARY keyword                                            | Convert to global temporary table                              | Same as left                         |
| [2](#2)  | create_definition: {<br>col_name column_definition<br>&#124; {INDEX &#124; KEY} [index_name] [index_type] (key_part,...)<br>[index_option] ... (table index)<br>&#124; {FULLTEXT &#124; SPATIAL} [INDEX &#124; KEY] [index_name] (key_part,...)<br>[index_option] ...<br>&#124; [CONSTRAINT [symbol]] PRIMARY KEY (table primary key)<br>[index_type] (key_part,...)<br>[index_option] ...<br>&#124; [CONSTRAINT [symbol]] UNIQUE [INDEX &#124; KEY] (index definition)<br>[index_name] [index_type] (key_part,...)<br>[index_option] ...<br>&#124; [CONSTRAINT [symbol]] FOREIGN KEY (foreign key reference definition)<br>[index_name] (col_name,...)<br>reference_definition (foreign key reference definition)<br>&#124; check_constraint_definition<br>} | Only retain col_name column_definition                          | Same as left                         |
| [3](#3)  | column_definition: {<br>data_type [NOT NULL &#124; NULL] [DEFAULT {literal &#124; (expr)} ]<br>[VISIBLE &#124; INVISIBLE]<br>[AUTO_INCREMENT] [UNIQUE [KEY]] [[PRIMARY] KEY]<br>[COMMENT 'string'] (column comment information)<br>[COLLATE collation_name]<br>[COLUMN_FORMAT {FIXED &#124; DYNAMIC &#124; DEFAULT}]<br>[ENGINE_ATTRIBUTE [=] 'string']<br>[SECONDARY_ENGINE_ATTRIBUTE [=] 'string']<br>[STORAGE {DISK &#124; MEMORY}]<br>[reference_definition]<br>[check_constraint_definition]<br>&#124; data_type<br>[COLLATE collation_name]<br>[GENERATED ALWAYS] AS (expr)<br>[VIRTUAL &#124; STORED] [NOT NULL &#124; NULL]<br>[VISIBLE &#124; INVISIBLE]<br>[UNIQUE [KEY]] [[PRIMARY] KEY]<br>[COMMENT 'string']<br>[reference_definition]<br>[check_constraint_definition]<br>} | Only retain data_type and DEFAULT. <br/>AUTO_INCREMENT and comment syntax will be converted to separate statements, appearing before and after the table definition respectively. <br/>Note: Only some data types can be correctly converted. | Same as left                         |
| [4](#4)  | table_option: {<br>AUTOEXTEND_SIZE [=] value<br>&#124; AUTO_INCREMENT [=] value<br>&#124; AVG_ROW_LENGTH [=] value<br>&#124; [DEFAULT] CHARACTER SET [=] charset_name<br>&#124; CHECKSUM [=] {0 &#124; 1}<br>&#124; [DEFAULT] COLLATE [=] collation_name<br>&#124; COMMENT [=] 'string'<br>&#124; COMPRESSION [=] {'ZLIB' &#124; 'LZ4' &#124; 'NONE'}<br>&#124; CONNECTION [=] 'connect_string'<br>&#124; {DATA &#124; INDEX} DIRECTORY [=] 'absolute path to directory'<br>&#124; DELAY_KEY_WRITE [=] {0 &#124; 1}<br>&#124; ENCRYPTION [=] {'Y' &#124; 'N'}<br>&#124; ENGINE [=] engine_name<br>&#124; ENGINE_ATTRIBUTE [=] 'string'<br>&#124; INSERT_METHOD [=] { NO &#124; FIRST &#124; LAST }<br>&#124; KEY_BLOCK_SIZE [=] value<br>&#124; MAX_ROWS [=] value<br>&#124; MIN_ROWS [=] value<br>&#124; PACK_KEYS [=] {0 &#124; 1 &#124; DEFAULT}<br>&#124; PASSWORD [=] 'string'<br>&#124; ROW_FORMAT [=] {DEFAULT &#124; DYNAMIC &#124; FIXED &#124; COMPRESSED &#124; REDUNDANT &#124; COMPACT}<br>&#124; START TRANSACTION <br>&#124; SECONDARY_ENGINE_ATTRIBUTE [=] 'string'<br>&#124; STATS_AUTO_RECALC [=] {DEFAULT &#124; 0 &#124; 1}<br>&#124; STATS_PERSISTENT [=] {DEFAULT &#124; 0 &#124; 1}<br>&#124; STATS_SAMPLE_PAGES [=] value<br>&#124; tablespace_option<br>&#124; UNION [=] (tbl_name[,tbl_name]...)<br>} | AUTO_INCREMENT [=] value will be replaced with the starting value of sequence defined before the table.<br><br>COMMENT [=] 'string' will be converted to a separate comment statement after the table.<br><br>Initial retrieval of tablespace_option may contain comments; remove comments (YMP processing), the tablespace will be normally converted (retain name). | Same as left                         |
| [5](#5)  | tablespace_option:<br>TABLESPACE tablespace_name [STORAGE DISK]<br>&#124; [TABLESPACE tablespace_name] STORAGE MEMORY | Only retain the tablespace name, remove [STORAGE DISK].<br><br>If the tablespace name is innodb_file_per_table, it indicates the default tablespace; thus, the tablespace name information will be discarded during conversion. | Same as left                         |
| [6](#6)  | partition_definition:<br>PARTITION partition_name partition definition syntax<br>[VALUES<br>{LESS THAN {(expr &#124; value_list) &#124; MAXVALUE}<br>&#124;<br>IN (value_list)}]<br>[[STORAGE] ENGINE [=] engine_name]<br>[COMMENT [=] 'string' ]<br>[DATA DIRECTORY [=] 'data_dir']<br>[INDEX DIRECTORY [=] 'index_dir']<br>[MAX_ROWS [=] max_number_of_rows]<br>[MIN_ROWS [=] min_number_of_rows]<br>[TABLESPACE [=] tablespace_name]<br>[(subpartition_definition [, subpartition_definition] ...)] | Only retain partition definition information supported by YashanDB,<br><br>[[STORAGE] ENGINE [=] engine_name]<br>[COMMENT [=] 'string' ]<br>[DATA DIRECTORY [=] 'data_dir']<br>[INDEX DIRECTORY [=] 'index_dir']<br>[MAX_ROWS [=] max_number_of_rows]<br>[MIN_ROWS [=] min_number_of_rows]<br><br>Will all be deleted.<br><br>Initial retrieval of partition information may contain comments; remove comments (YMP processing). | Same as left                         |
| [7](#7)  | subpartition_definition: subpartition definition syntax<br><br>SUBPARTITION logical_name<br><br>[[STORAGE] ENGINE [=] engine_name]<br><br>[COMMENT [=] 'string' ]<br><br>[DATA DIRECTORY [=] 'data_dir']<br><br>[INDEX DIRECTORY [=] 'index_dir']<br><br>[MAX_ROWS [=] max_number_of_rows]<br><br>[MIN_ROWS [=] min_number_of_rows]<br><br>[TABLESPACE [=] tablespace_name] | The syntax does not exist in 22.2, deleted during conversion.                                   | 23.1 retains the subpartition conversion output |
| [8](#8)  | ON UPDATE CURRENT_TIMESTAMP(n)                              | Converted to trigger                                          | Same as left                         |

## CREATE VIEW

|No. |MySQL Syntax/Keyword |YashanDB 22.2 Transformation Rule |YashanDB 23.1 Transformation Rule |
| --------- | ------------------------------------------------------------ | ------------------------------------------- | -------------------- |
| [9](#9)   | CREATE<br>[OR REPLACE]<br>[SQL SECURITY { DEFINER &#124; INVOKER }]<br>VIEW view_name [(column_list)]<br>AS | Remove [SQL SECURITY { DEFINER &#124; INVOKER }]    | Same as left                       |
| [10](#10) | [WITH [CASCADED &#124; LOCAL] CHECK OPTION]                  | Completely removed                                     | Same as left                       |
| 11        | SELECT subquery                                               | Maintain semantic output                              | Same as left                       |

## CREATE INDEX

|No. |MySQL Syntax/Keyword |YashanDB 22.2 Transformation Rule |YashanDB 23.1 Transformation Rule |
| --------- | ------------------------------------------------------------ | -------------------------- | -------------------- |
| [12](#12) | CREATE [UNIQUE &#124; FULLTEXT &#124; SPATIAL] INDEX index_name | Only UNIQUE type and regular type are supported | Same as left                       |
| [13](#13) | index_option: {<br>KEY_BLOCK_SIZE [=] value<br>&#124; index_type<br>&#124; WITH PARSER parser_name<br>&#124; COMMENT 'string'<br>&#124; {VISIBLE &#124; INVISIBLE}<br>&#124; ENGINE_ATTRIBUTE [=] 'string'<br>&#124; SECONDARY_ENGINE_ATTRIBUTE [=] 'string'<br>}<br>index_type:<br>USING {BTREE &#124; HASH}<br>algorithm_option:<br>ALGORITHM [=] {DEFAULT &#124; INPLACE &#124; COPY}<br>lock_option:<br>LOCK [=] {DEFAULT &#124; NONE &#124; SHARED &#124; EXCLUSIVE} | All deleted                         | Same as left                       |

## ALTER TABLE ADD CONSTRAINT / MODIFY NOT NULL

|No. |MySQL Syntax/Keyword |YashanDB 22.2 Transformation Rule |YashanDB 23.1 Transformation Rule |
| --------- | ------------------------------------------------------------ | ----------------------------- | -------------------- |
| [14](#14) | [CONSTRAINT [symbol]] PRIMARY KEY<br><br>[index_type] (key_part,...)<br><br>[index_option] ... | Remove index_type and index_option             | Same as left                       |
| [15](#15) | ADD [CONSTRAINT [symbol]] UNIQUE [INDEX &#124; KEY]<br>[index_name] [index_type] (key_part,...)<br>[index_option] ... | Remove index_type and index_option             | Same as left                       |
| 16        | ADD [CONSTRAINT [symbol]] FOREIGN KEY<br>[index_name] (col_name,...)<br>reference_definition | Consistent                                      | Same as left                       |
| [17](#17) | ADD [CONSTRAINT [symbol]] CHECK (expr) [[NOT] ENFORCED]   | Remove [[NOT] ENFORCED]                       | Same as left                       |
| [18](#18) | MODIFY COLUMN_NAME DATATYPE NOT NULL                         | Remove DATATYPE                                 | Same as left                       |

## CREATE EVENT

The MySQL Event corresponds to the JOB object in YashanDB. By querying the information_schema.EVENTS view, necessary key information can be assembled to form the creation statement for a YashanDB JOB. 

The key fields and their meanings from the information_schema.EVENTS are shown in the table below.

| EVENT_SCHEMA  | EVENT_NAME | EVENT_DEFINITION | EVENT_TYPE      | EXECUTE_AT | INTERVAL_VALUE | INTERVAL_FIELD | STARTS         | ENDS           | STATUS | ON_COMPLETION | EVENT_COMMENT |
|---------------|------------|------------------|-----------------|------------|----------------|----------------|----------------|----------------|--------|---------------|---------------|
| Event schema ownership | Event name | SQL statement to execute | Event type (repeat/one-time) | Execution time | Execution interval value | Unit of the execution interval time | Time when the event starts to take effect | Time when the event stops taking effect | Object status | Does it get deleted after completion | Comment information of the object |  

By concatenating the above key information, the corresponding YashanDB JOB creation statement can be obtained. The format for creating a YashanDB JOB is as follows:

```sql
EXEC DBMS_SCHEDULER.CREATE_JOB (
    job_name IN VARCHAR,   -- Corresponding field EVENT_NAME
    job_type IN VARCHAR,    -- Corresponding field EVENT_TYPE
    job_action IN VARCHAR , -- Corresponding field begin EVENT_DEFINITION end ;
    number_of_arguments IN INTEGER DEFAULT 0,   -- No corresponding field retain field, using default value 0.
    start_date IN TIMESTAMP DEFAULT NULL,   -- Corresponding field STARTS / EXECUTE_AT
    repeat_interval IN VARCHAR DEFAULT NULL,    -- Corresponding field INTERVAL_VALUE INTERVAL_FIELD
    end_date IN TIMESTAMP DEFAULT NULL, -- Corresponding field ENDS
    job_class IN VARCHAR DEFAULT 'DEFAULT_JOB_CLASS',   -- No corresponding field retain field, using default value 'DEFAULT_JOB_CLASS'.
    enabled IN BOOLEAN DEFAULT FALSE,   -- Corresponding field STATUS
    auto_drop IN BOOLEAN DEFAULT TRUE,  -- Corresponding field ON_COMPLETION
    comments IN VARCHAR DEFAULT NULL);  -- Corresponding field EVENT_COMMENT
```

Example [19](#19) demonstrates a MySQL Event converted to a YashanDB JOB.

## PLSQL Object Handling

|Object Type |Rule |MySQL Syntax |YashanDB Expected |
|-----------|-------------|-------------------------------------------------------------|-------------------------------------------|
| Trigger           |               | CREATE [DEFINER = user] TRIGGER [IF NOT EXISTS] sp_name   | CREATE or replace TRIGGER "SP_NAME"         |
| Function          |               | CREATE [DEFINER = user] FUNCTION [IF NOT EXISTS] sp_name  | CREATE or replace FUNCTION "SP_NAME"        |
| Stored Procedure   |               | CREATE [DEFINER = user] PROCEDURE [IF NOT EXISTS] sp_name | CREATE or replace PROCEDURE "SP_NAME"       |
| Object name surrounded by ` | Replace with " and convert to uppercase | ```sql create table `xxx` (`id` int );```        | ```sql create table "XXX" ("ID" int );``` |

## All Examples

<span id="1" name="1" class="yaslink"></span>

### Example 1

```sql
 -- MySQL
 CREATE TEMPORARY TABLE x(id INT)
 
 -- YashanDB 22.2 Expected Result
 CREATE GLOBAL TEMPORARY TABLE "X" ("ID" INT);
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="2" name="2" class="yaslink"></span>

### Example 2

```sql
 -- MySQL
 CREATE TABLE MYL.TEST4 (col1 INT REFERENCES MYL.T2(col1) ON DELETE SET null, col2 INT DEFAULT 1 CHECK(col2 < 30));
 
 -- YashanDB 22.2 Expected Result
 CREATE TABLE "MYL"."TEST4" (
    "COL1" INT,
    "COL2" INT DEFAULT 1
);
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="3" name="3" class="yaslink"></span>

### Example 3

```sql
 -- MySQL
CREATE TABLE x(id INT auto_increment DEFAULT 'xxx' COMMENT 'xxx');

 -- YashanDB 22.2 Expected Result
 CREATE SEQUENCE "X_ID_SEQ" INCREMENT BY 1 START WITH 1;
CREATE TABLE "X" (
    "ID" INT DEFAULT 'xxx' DEFAULT "X_ID_SEQ".NEXTVAL
);
 
COMMENT ON COLUMN "X"."ID" IS 'xxx';
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="4" name="4" class="yaslink"></span>

### Example 4

```sql
 -- MySQL
CREATE TABLE x(id INT auto_increment DEFAULT 'xxx' COMMENT 'xxx') COMMENT 'xxx' auto_increment = 1231 TABLESPACE xxx STORAGE DISK;

 -- YashanDB 22.2 Expected Result
 CREATE SEQUENCE "X_ID_SEQ" INCREMENT BY 1 START WITH 1231;
CREATE TABLE x (
    "ID" INT DEFAULT 'xxx' DEFAULT "X_ID_SEQ".NEXTVAL
) TABLESPACE "XXXDISK";
 
COMMENT ON COLUMN "X"."ID" IS 'xxx';
COMMENT ON TABLE "X" IS 'xxx';
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="5" name="5" class="yaslink"></span>

### Example 5

```sql
 -- MySQL
 CREATE TABLE x(id INT auto_increment DEFAULT 'xxx' COMMENT 'xxx') COMMENT 'xxx' auto_increment = 1231 TABLESPACE xxx STORAGE DISK;
 
 -- YashanDB 22.2 Expected Result
 CREATE SEQUENCE "X_ID_SEQ" INCREMENT BY 1 START WITH 1231;
CREATE TABLE "x" (
    "ID" INT DEFAULT 'xxx' DEFAULT "X_ID_SEQ".NEXTVAL
) TABLESPACE "XXXDISK";
 
COMMENT ON COLUMN "X"."ID" IS 'xxx';
COMMENT ON TABLE "X" IS 'xxx';
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="6" name="6" class="yaslink"></span>

### Example 6

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
 -- YashanDB 22.2 Expected Result
 CREATE TABLE "TH" (
    "ID" INT,
    "NAME" VARCHAR(30 CHAR),
    "ADATE" DATE
)
PARTITION BY LIST (YEAR("ADATE")) ( PARTITION "P1999" VALUES ((1995), (1999), (2003)) , PARTITION "P2000" VALUES ((1996), (2000), (2004))
    TABLESPACE "XXX", PARTITION "P2001" VALUES ((1997), (2001), (2005)) , PARTITION "P2002" VALUES ((1998), (2002), (2006))
);
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="7" name="7" class="yaslink"></span>

### Example 7

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
 -- YashanDB 22.2 Expected Result
 CREATE TABLE "TS" (
    "ID" INT,
    "PURCHASED" DATE
)
PARTITION BY RANGE (YEAR("PURCHASED"))
 ( PARTITION "P0" VALUES LESS THAN (1990) , PARTITION "P1" VALUES LESS THAN (2000) , PARTITION "P2" VALUES LESS THAN (MAXVALUE) );
 -- YashanDB 23.1 Expected Result
 CREATE TABLE "TS" (
    "ID" INT,
    "PURCHASED" DATE
)
PARTITION BY RANGE (YEAR("PURCHASED"))
SUBPARTITION BY HASH(TO_DAYS("PURCHASED")) SUBPARTITIONS  2
 ( PARTITION "P0" VALUES LESS THAN (1990) , PARTITION "P0" VALUES LESS THAN (2000) , PARTITION "P2" VALUES LESS THAN (MAXVALUE) );
 -- Same as above
```

<span id="8" name="8" class="yaslink"></span>

### Example 8

```sql
 -- MySQL
CREATE TABLE `test_timestamp`(
    id INT , 
    t1 TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP(3) ON UPDATE CURRENT_TIMESTAMP(3))
 -- YashanDB 22.2 Expected Result
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
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="9" name="9" class="yaslink"></span>

### Example 9

```sql
 -- MySQL
 CREATE OR replace  VIEW v3 (idalias ) AS SELECT id FROM v1 WHERE a > 0
 -- YashanDB 22.2 Expected Result
 CREATE OR REPLACE VIEW "V3"
AS
SELECT "ID"
FROM "V1"
WHERE "A" > 0;
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="10" name="10" class="yaslink"></span>

### Example 10

```sql
 -- MySQL
 CREATE OR replace  VIEW v3 (idalias ) AS SELECT id FROM v1 WHERE a > 0
WITH CASCADED CHECK OPTION;
 -- YashanDB 22.2 Expected Result
 CREATE OR replace  VIEW "V3" ("IDALIAS" ) AS SELECT "ID" FROM "V1" WHERE "A" > 0

 -- YashanDB 23.1 Expected Result
 -- Same AS above
```

<span id="12" name="12" class="yaslink"></span>

### Example 12

```sql
 -- MySQL
 /*The 1 SQL statement*/
CREATE UNIQUE INDEX xxx ON x(x);
 
/*The 2 SQL statement*/
CREATE FULLTEXT INDEX xxx ON x(x);
 
/*The 3 SQL statement*/
CREATE INDEX xxx ON x(x);

 -- YashanDB 22.2 Expected Result
 /*The 1 SQL statement*/
CREATE UNIQUE INDEX "XXX" ON "X" ("X");
 
/*The 2 SQL statement*/
CREATE  INDEX "XXX" ON "X" ("X");
 
/*The 3 SQL statement*/
CREATE  INDEX "XXX" ON "X" ("X");
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="13" name="13" class="yaslink"></span>

### Example 13

```sql
 -- MySQL
 /*The 1 SQL statement*/
CREATE INDEX id_index ON lookup (id) USING BTREE;
 
/*The 2 SQL statement*/
CREATE INDEX id_index ON t1 (id) COMMENT 'MERGE_THRESHOLD=40';

 -- YashanDB 22.2 Expected Result
 /*The 1 SQL statement*/
CREATE INDEX "ID_INDEX" ON "LOOKUP" ("ID");
 
/*The 2 SQL statement*/
CREATE INDEX "ID_INDEX" ON "T1" ("ID");
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="14" name="14" class="yaslink"></span>

### Example 14

```sql
 -- MySQL
 ALTER TABLE xx ADD PRIMARY USING BTREE (id);
 -- YashanDB 22.2 Expected Result
 ALTER TABLE "XX" ADD PRIMARY KEY ("ID");
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="15" name="15" class="yaslink"></span>

### Example 15

```sql
 -- MySQL
 ALTER TABLE student ADD CONSTRAINT  xx UNIQUE KEY USING BTREE(id)  ;
 -- YashanDB 22.2 Expected Result
 ALTER TABLE "STUDENT" ADD CONSTRAINT "XX" UNIQUE ("ID");
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="17" name="17" class="yaslink"></span>

### Example 17

```sql
 -- MySQL
ALTER TABLE student ADD CHECK (id>0) ENFORCED ;
 -- YashanDB 22.2 Expected Result
 ALTER TABLE "STUDENT" ADD CHECK ("ID">0) ;
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="18" name="18" class="yaslink"></span>

### Example 18

```sql
 -- MySQL
 ALTER TABLE X MODIFY ID INT NOT NULL;
 -- YashanDB 22.2 Expected Result
 ALTER TABLE "X" MODIFY "ID"  NOT NULL;
 -- YashanDB 23.1 Expected Result
 -- Same as above
```

<span id="19" name="19" class="yaslink"></span>

### Example 19

```sql
 -- MySQL
 CREATE DEFINER=`root`@`%` EVENT `myevent` 
    ON SCHEDULE AT '2024-01-09 06:14:00' 
    ON COMPLETION PRESERVE DISABLE ON SLAVE 
    DO 
        UPDATE mytable SET mycol = mycol + 1;
    ;   
 -- YashanDB 22.2 Expected Result
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
 -- YashanDB 23.1 Expected Result
 -- Same as above
```
