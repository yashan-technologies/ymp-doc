This document summarizes the conversion rules involved in YMP's compatibility transformation of DDL from the source end.

## Metadata Transformation Rules

|No. |Conversion Rule |Affected Object |Description |
| --------- | ----------------------------------- | ---------------------------- |-------------------------------------------------------------------------|
| 1         | Manual Modification                     | --                           | User manually modifies or batch modifies the DDL on the evaluation interface                                      |
| 2         | Remove NOT NULL Constraint              | Table                        | Both empty strings and null are considered null in YashanDB, remove NOT NULL constraint if it violates during migration of empty strings                         |
| [3](#3)   | Data Type Conversion                    | Table                        | This data type does not exist in YashanDB and needs conversion          |
| [4](#4)   | Convert Auto Increment Column to Sequence | Table                        | MySQL's AUTO_INCREMENT (or Oracle and DM's auto-increment columns) are converted to YashanDB's sequence                     |
| [5](#5)   | Convert ON UPDATE CURRENT_TIMESTAMP to Trigger | Table                        | MySQL's ON UPDATE CURRENT_TIMESTAMP is converted to YashanDB's trigger                         |
| 6         | Derivative Objects                      | Sequence, Trigger            | Auto increment column converted to sequence, MySQL's ON UPDATE CURRENT_TIMESTAMP syntax converted to trigger                          |
| 7         | Modify Sequence Starting Value          | Sequence                     | Batch modification of sequence starting value in YMP interface         |
| [8](#8)   | Convert Temporary Table to Global Temporary Table | Table                   | MySQL temporary tables are converted to YashanDB's public TEMPORARY                                    |
| [9](#9)   | Remove Version Comments                 | Table                        | Remove version comments in MySQL table DDL, such as `/*!50500 */`, visible examples                                      |
| [10](#10) | Remove Table Parameters                  | Table                        | Remove unsupported table parameter syntax in YashanDB from table DDL                                               |
| [11](#11) | Remove Column Parameters                 | Table                        | Remove unsupported column parameter syntax in YashanDB from table DDL                                               |
| [12](#12) | Remove Partition Information            | Table                        | Remove unsupported partition parameter syntax from partition table DDL in YashanDB                                           |
| [13](#13) | Remove Subpartition                     | Table                        | Only supported in YashanDB 22.2 version; YashanDB 22.2 does not support subpartition in partition tables, DM has no such rule                       |
| [14](#14) | Extract Constraints as Separate Objects  | Table                        | Constraints (not null, primary key, unique, check, foreign key) in table DDL are extracted as separate objects            |
| [15](#15) | Extract Comments as Separate Statements  | Table                        | Comments in table DDL are extracted as separate DDL statements, DM has no such rule                              |
| [16](#16) | Remove View Creation Prefix Information | View                         | Remove view creation prefix information from MySQL [SQL SECURITY { DEFINER \                                 |
| [17](#17) | Remove View Creation Suffix Information | View                         | Remove view creation suffix information from MySQL [WITH [CASCADED \                                         |
| [18](#18) | Convert EVENT to JOB                    | EVENT                        | MySQL's EVENT is converted to YashanDB's JOB                                              |
| [19](#19) | Remove User Information                 | View, Trigger, Stored Procedure, Function | MySQL's initial DDL contains ALGORITHM=UNDEFINED DEFINER=`root`@`%`, needs to be removed               |
| [20](#20) | Remove NOLOGGING                        | Table, Index                | In YMP's creation task interface, it can be set whether to ignore NOLOGGING parameter; set to true, it will remove NOLOGGING parameter from Oracle tables and indexes                 |
| [21](#21) | Remove Index Parameters                  | Index                        | Some index parameters from Oracle and DM are not supported in YashanDB and need to be removed for compatibility                                 |
| [22](#22) | Remove Sequence Parameters                | Sequence                     | Oracle's sequence parameters (NOKEEP, NOSCALE, GLOBAL) and DM's sequence parameters (LOCAL) are not supported in YashanDB and need to be removed for compatibility |

## All Examples

<span id="3" name="3" class="yaslink"></span>

#### Example 3

```sql
-- MySQL
CREATE TABLE `data_type_test` (
  `id` mediumint DEFAULT NULL,
  `id01` DECIMAL(10,0) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci

-- YashanDB
CREATE TABLE "DATA_TYPE_TEST" (
	"ID" INT DEFAULT NULL,
	"ID01" NUMBER DEFAULT NULL
);
-- Note: mediumint is converted to INT type, decimal(10,0) is converted to NUMBER type
```

<span id="4" name="4" class="yaslink"></span>

#### Example 4

```sql
-- MySQL
CREATE TABLE `auto_increment_tab` (
  `id` INT NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

-- YashanDB
CREATE TABLE "AUTO_INCREMENT_TAB" (
	"ID" INT DEFAULT "AUTO_INCREMENT_TAB_ID_SEQ".NEXTVAL 
);
-- Note: AUTO_INCREMENT is converted to "AUTO_INCREMENT_TAB_ID_SEQ".NEXTVAL

-- Oracle
CREATE TABLE "TEST"."TB_TEST" 
   (	"ID" NUMBER GENERATED ALWAYS AS IDENTITY MINVALUE 1 MAXVALUE 9999999999999999999999999999 INCREMENT BY 1 START WITH 1 CACHE 20 NOORDER  NOCYCLE  NOKEEP  NOSCALE , 
	"NAME" VARCHAR2(100)
   ) SEGMENT CREATION DEFERRED 
  PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255 
 NOCOMPRESS LOGGING
  TABLESPACE "USERS" ;
  
-- YashanDB
CREATE TABLE "TEST"."TB_TEST" (
	"ID" NUMBER DEFAULT "TEST"."TB_TEST_ID_SEQ".NEXTVAL ,
	"NAME" VARCHAR(100)
)
PCTFREE 10
PCTUSED 40
INITRANS 1
MAXTRANS 255
NOCOMPRESS
LOGGING
TABLESPACE "USERS";
-- Note: GENERATED ALWAYS AS IDENTITY is converted to "TEST"."TB_TEST_ID_SEQ".NEXTVAL
```

<span id="5" name="5" class="yaslink"></span>

#### Example 5

```sql
-- MySQL
CREATE TABLE `on_update_test` (
  `t1` TIMESTAMP(3) NULL DEFAULT CURRENT_TIMESTAMP(3) ON UPDATE CURRENT_TIMESTAMP(3)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci

-- YashanDB
CREATE TABLE "ON_UPDATE_TEST" (
	"T1" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP()
) ;
-- Note: ON UPDATE CURRENT_TIMESTAMP(3) is converted to a derived trigger
```

<span id="8" name="8" class="yaslink"></span>

#### Example 8

```sql
-- MySQL
CREATE TEMPORARY TABLE `temp_table` (
  `id` INT DEFAULT NULL,
  `name` VARCHAR(20) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci

-- YashanDB
CREATE GLOBAL TEMPORARY TABLE "TEMP_TABLE" (
	"ID" INT DEFAULT NULL,
	"NAME" VARCHAR(20 CHAR) DEFAULT NULL
);
-- Note: TEMPORARY is converted to GLOBAL TEMPORARY
```

<span id="9" name="9" class="yaslink"></span>

#### Example 9

```sql
-- MySQL
CREATE TABLE `partition_option` (
  `empno` VARCHAR(20) NOT NULL,
  `empname` VARCHAR(20) DEFAULT NULL,
  `deptno` INT DEFAULT NULL,
  `birthdate` year NOT NULL,
  `salary` INT DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci MIN_ROWS=1 MAX_ROWS=100
/*!50100 PARTITION BY RANGE (`birthdate`)
(PARTITION p1 VALUES LESS THAN (1980) MAX_ROWS = 100 MIN_ROWS = 1 ENGINE = InnoDB,
 PARTITION p2 VALUES LESS THAN (1990) MAX_ROWS = 100 MIN_ROWS = 1 ENGINE = InnoDB,
 PARTITION p3 VALUES LESS THAN MAXVALUE MAX_ROWS = 100 MIN_ROWS = 1 ENGINE = InnoDB) */
 
-- YashanDB
CREATE TABLE "PARTITION_OPTION" (
	"EMPNO" VARCHAR(20 CHAR),
	"EMPNAME" VARCHAR(20 CHAR) DEFAULT NULL,
	"DEPTNO" INT DEFAULT NULL,
	"BIRTHDATE" SMALLINT,
	"SALARY" INT DEFAULT NULL
)
PARTITION BY RANGE ("BIRTHDATE")
 ( PARTITION "P1" VALUES LESS THAN (1980) , PARTITION "P2" VALUES LESS THAN (1990) , PARTITION "P3" VALUES LESS THAN (MAXVALUE) );
-- Note: Remove `/*!50100 */`
```

<span id="10" name="10" class="yaslink"></span>

#### Example 10

```sql
-- MySQL
CREATE TABLE `auto_increment_tab` (
  `id` INT NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci

-- YashanDB
CREATE TABLE "AUTO_INCREMENT_TAB" (
	"ID" INT DEFAULT "AUTO_INCREMENT_TAB_ID_SEQ".NEXTVAL 
)
;
-- Note: Removed ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
```

<span id="11" name="11" class="yaslink"></span>

#### Example 11

```sql
-- MySQL
CREATE TABLE `remove_col_option` (
  `id` INT NOT NULL /*!50606 COLUMN_FORMAT FIXED */ 
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci

-- YashanDB
CREATE TABLE "REMOVE_COL_OPTION" (
	"ID" INT
)
;
-- Note: Remove COLUMN_FORMAT FIXED
```

<span id="12" name="12" class="yaslink"></span>

#### Example 12

```sql
-- MySQL
CREATE TABLE `partition_option` (
  `empno` VARCHAR(20) NOT NULL,
  `empname` VARCHAR(20) DEFAULT NULL,
  `deptno` INT DEFAULT NULL,
  `birthdate` year NOT NULL,
  `salary` INT DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci MIN_ROWS=1 MAX_ROWS=100
/*!50100 PARTITION BY RANGE (`birthdate`)
(PARTITION p1 VALUES LESS THAN (1980) MAX_ROWS = 100 MIN_ROWS = 1 ENGINE = InnoDB,
 PARTITION p2 VALUES LESS THAN (1990) MAX_ROWS = 100 MIN_ROWS = 1 ENGINE = InnoDB,
 PARTITION p3 VALUES LESS THAN MAXVALUE MAX_ROWS = 100 MIN_ROWS = 1 ENGINE = InnoDB) */

-- YashanDB
CREATE TABLE "PARTITION_OPTION" (
	"EMPNO" VARCHAR(20 CHAR),
	"EMPNAME" VARCHAR(20 CHAR) DEFAULT NULL,
	"DEPTNO" INT DEFAULT NULL,
	"BIRTHDATE" SMALLINT,
	"SALARY" INT DEFAULT NULL
)
PARTITION BY RANGE ("BIRTHDATE")
 ( PARTITION "P1" VALUES LESS THAN (1980) , PARTITION "P2" VALUES LESS THAN (1990) , PARTITION "P3" VALUES LESS THAN (MAXVALUE) )
;
-- Note: Removed partition parameters MAX_ROWS = 100 MIN_ROWS = 1 ENGINE = InnoDB
```

<span id="13" name="13" class="yaslink"></span>

#### Example 13

```sql
-- MySQL
CREATE TABLE `tb_sub` (
  `id` INT DEFAULT NULL,
  `purchased` year DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
PARTITION BY RANGE (`purchased`)
SUBPARTITION BY HASH (`purchased`)
SUBPARTITIONS 2
(PARTITION p0 VALUES LESS THAN (1990) ENGINE = InnoDB,
 PARTITION p1 VALUES LESS THAN (2000) ENGINE = InnoDB,
 PARTITION p2 VALUES LESS THAN MAXVALUE ENGINE = InnoDB)
 
-- YashanDB 22.2
 CREATE TABLE "TB_SUB" (
	"ID" INT DEFAULT NULL,
	"PURCHASED" SMALLINT DEFAULT NULL
)
PARTITION BY RANGE ("PURCHASED")
 ( PARTITION p0 VALUES LESS THAN (1990) , PARTITION p1 VALUES LESS THAN (2000) , PARTITION p2 VALUES LESS THAN (MAXVALUE) )
;
-- Note: Removed subpartition SUBPARTITION
```

<span id="14" name="14" class="yaslink"></span>

#### Example 14

```sql
-- MySQL
CREATE TABLE `remove_tab_option` (
  `id` INT NOT NULL ,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1231 DEFAULT CHARSET=utf8mb3 COMMENT='xxx'

-- YashanDB
CREATE TABLE "REMOVE_TAB_OPTION" (
	"ID" INT 
);
-- Note: Extract NOT NULL constraint as a separate object
```

<span id="15" name="15" class="yaslink"></span>

#### Example 15

```sql
-- MySQL
CREATE TABLE `remove_tab_option` (
  `id` INT COMMENT 'xxx',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1231 DEFAULT CHARSET=utf8mb3 COMMENT='xxx'

-- YashanDB
CREATE TABLE "REMOVE_TAB_OPTION" (
	"ID" INT 
)
;
COMMENT ON COLUMN "REMOVE_TAB_OPTION"."ID" IS 'xxx';
COMMENT ON TABLE "REMOVE_TAB_OPTION" IS 'xxx';
-- Note: Extract column comments and table comments as separate statements
```

<span id="16" name="16" class="yaslink"></span>

#### Example 16

```sql
-- MySQL
CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`%` SQL SECURITY DEFINER VIEW `test1702`.`test_view` AS SELECT `test1702`.`partition_option`.`empno` AS `empno`,`test1702`.`partition_option`.`empname` AS `empname`,`test1702`.`partition_option`.`deptno` AS `deptno`,`test1702`.`partition_option`.`birthdate` AS `birthdate`,`test1702`.`partition_option`.`salary` AS `salary` FROM `test1702`.`partition_option`

-- YashanDB
CREATE VIEW "TEST1702"."TEST_VIEW"
AS
SELECT "TEST1702"."PARTITION_OPTION"."EMPNO" AS "EMPNO", "TEST1702"."PARTITION_OPTION"."EMPNAME" AS "EMPNAME", "TEST1702"."PARTITION_OPTION"."DEPTNO" AS "DEPTNO", "TEST1702"."PARTITION_OPTION"."BIRTHDATE" AS "BIRTHDATE", "TEST1702"."PARTITION_OPTION"."SALARY" AS "SALARY"
FROM "TEST1702"."PARTITION_OPTION";
-- Note: Remove SQL SECURITY DEFINER
```

<span id="17" name="17" class="yaslink"></span>

#### Example 17

```sql
-- MySQL
CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`%` SQL SECURITY DEFINER VIEW `test1702`.`test_view1` AS SELECT `test1702`.`partition_option`.`empno` AS `empno`,`test1702`.`partition_option`.`empname` AS `empname`,`test1702`.`partition_option`.`deptno` AS `deptno`,`test1702`.`partition_option`.`birthdate` AS `birthdate`,`test1702`.`partition_option`.`salary` AS `salary` FROM `test1702`.`partition_option` WITH CASCADED CHECK OPTION

-- YashanDB
CREATE VIEW "TEST1702"."TEST_VIEW1"
AS
SELECT "TEST1702"."PARTITION_OPTION"."EMPNO" AS "EMPNO", "TEST1702"."PARTITION_OPTION"."EMPNAME" AS "EMPNAME", "TEST1702"."PARTITION_OPTION"."DEPTNO" AS "DEPTNO", "TEST1702"."PARTITION_OPTION"."BIRTHDATE" AS "BIRTHDATE", "TEST1702"."PARTITION_OPTION"."SALARY" AS "SALARY"
FROM "TEST1702"."PARTITION_OPTION";
-- Note: Remove WITH CASCADED CHECK OPTION
```

<span id="18" name="18" class="yaslink"></span>

#### Example 18

```sql
-- MySQL
CREATE DEFINER=`root`@`%` EVENT `myevent` ON SCHEDULE AT '2024-02-22 02:44:02' ON COMPLETION NOT PRESERVE ENABLE DO UPDATE myschema.mytable SET mycol = mycol + 1

-- YashanDB
EXEC DBMS_SCHEDULER.CREATE_JOB('test1702.myevent',
'PLSQL_BLOCK', 
'BEGIN UPDATE myschema.mytable SET mycol = mycol + 1 ;  END ;' ,
0,
TO_TIMESTAMP('2024-02-22 02:44:02', 'YYYY-MM-DD HH24:MI:SS'),
null,
null,
'DEFAULT_JOB_CLASS',
false,
true,
'')
-- Note: MySQL's EVENT IS converted TO Yashandb JOB
```

<span id="19" name="19" class="yaslink"></span>

#### Example 19

```sql
-- MySQL
CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`%` SQL SECURITY DEFINER VIEW `test1702`.`test_view1` AS SELECT `test1702`.`partition_option`.`empno` AS `empno`,`test1702`.`partition_option`.`empname` AS `empname`,`test1702`.`partition_option`.`deptno` AS `deptno`,`test1702`.`partition_option`.`birthdate` AS `birthdate`,`test1702`.`partition_option`.`salary` AS `salary` FROM `test1702`.`partition_option` WITH CASCADED CHECK OPTION

-- YashanDB
CREATE VIEW "TEST1702"."TEST_VIEW1"
AS
SELECT "TEST1702"."PARTITION_OPTION"."EMPNO" AS "EMPNO", "TEST1702"."PARTITION_OPTION"."EMPNAME" AS "EMPNAME", "TEST1702"."PARTITION_OPTION"."DEPTNO" AS "DEPTNO", "TEST1702"."PARTITION_OPTION"."BIRTHDATE" AS "BIRTHDATE", "TEST1702"."PARTITION_OPTION"."SALARY" AS "SALARY"
FROM "TEST1702"."PARTITION_OPTION";
-- Note: Remove ALGORITHM=UNDEFINED DEFINER=`root`@`%`
```

<span id="20" name="20" class="yaslink"></span>

#### Example 20

```sql
-- Oracle
CREATE TABLE "TEST"."TAB004" 
   (	"ID" NUMBER(*,0)
   ) SEGMENT CREATION DEFERRED 
  PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255 
 NOCOMPRESS NOLOGGING
  TABLESPACE "USERS" ;
  
 CREATE INDEX "TEST"."IDX05" ON "TEST"."TAB004" ("ID") 
  PCTFREE 10 INITRANS 2 MAXTRANS 255 COMPUTE STATISTICS NOLOGGING 
  TABLESPACE "USERS" ;

-- YashanDB
CREATE TABLE "TEST"."TAB004" 
   (	"ID" NUMBER(*,0)
   ) SEGMENT CREATION DEFERRED 
  PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255 
 NOCOMPRESS  TABLESPACE "USERS" ;
 
CREATE INDEX "TEST"."IDX05" ON "TEST"."TAB004" ( "ID" ) 
PCTFREE 10
INITRANS 2
MAXTRANS 255
TABLESPACE "USERS";

-- Note: Remove NOLOGGING
```

<span id="21" name="21" class="yaslink"></span>

#### Example 21

```sql
-- Oracle
CREATE INDEX "TEST"."IDX01" ON "TEST"."PT_HASH_TEST" ("PID") 
  PCTFREE 10 INITRANS 2 MAXTRANS 255 COMPUTE STATISTICS 
  TABLESPACE "USERS" ;

-- YashanDB
CREATE INDEX "TEST"."IDX01" ON "TEST"."PT_HASH_TEST" ( "PID" ) 
PCTFREE 10
INITRANS 2
MAXTRANS 255
TABLESPACE "USERS";

-- Note: Remove COMPUTE STATISTICS 
```

<span id="22" name="22" class="yaslink"></span>

#### Example 22

```sql
-- Oracle
CREATE SEQUENCE  "TEST"."SEQ001"  MINVALUE 1 MAXVALUE 9999999999999999999999999999 INCREMENT BY 1 START WITH 1 CACHE 20 NOORDER  NOCYCLE  NOKEEP  NOSCALE  GLOBAL ;

-- YashanDB
CREATE SEQUENCE "TEST"."SEQ001" START WITH 1 INCREMENT BY 1 MAXVALUE 9999999999999999999999999999 MINVALUE 1 NOCYCLE CACHE 20 NOORDER;

-- Note: Remove NOKEEP  NOSCALE  GLOBAL 
```
