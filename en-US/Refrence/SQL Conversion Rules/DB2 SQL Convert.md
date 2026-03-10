This document standardizes the syntax based on existing DB2 syntax through SQL transformation.

## Overview

During the task assessment phase of YMP, a compatibility assessment of objects (tables, views, constraints, indexes, etc.) in the source database will be performed to ensure that the source database objects can be successfully migrated to the target database.  
In the assessment phase, an attempt will be made to obtain the DDL of the source database objects and execute it in the target database. If the execution is successful, this indicates that the object is natively compatible and does not require special handling. 
Otherwise, the SQL transformation tool will convert the DDL according to the syntax transformation rules from the source database to the target database to adapt to the syntax features of the target database.  
If the transformation is successful, an attempt will be made to execute the transformed SQL in the target database; if successful, this indicates that the object is automatically compatible and can be migrated.  
Due to the lack of a systematic method to obtain object DDL in DB2, the DDL is obtained by the DB2LOOK tool during the assessment phase, which may lead to unsupported syntax.

## CREATE TABLE

|No. |DB2 Syntax/Keywords |YashanDB23 Transformation Rules |
|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [1](#1) | lob-options: [ NOT ] LOGGED &#124; [ NOT ] COMPACT                                                                                                                                                                                                                  | Remove                                                       | 
| 2   | generated-clause: GENERATED ALWAYS AS IDENTITY [ START WITH ( numeric-constant &#124; 1 ) ] [ INCREMENT BY ( numeric-constant &#124; 1 ) ] [ NO MAXVALUE &#124; MAXVALUE numeric-constant ] [ NO MINVALUE &#124; MINVALUE numeric-constant ] [ [ NO ] CYCLE ] [ NO CACHE &#124; CACHE numeric-constant ] [ [ NO ] ORDER ] | Keep only for YashanDB version >= 23.4.2, convert others to sequence | 
| 3   | Table-level constraints                                                                                                                                                                                                                                                                  | Convert to independent constraint objects                     | 
| 4   | ORGANIZE BY ROW &#124; COLUMN                                                                                                                                                                                                                                                 | Remove                                                       | 
| 5   | COMPRESS NO &#124; YES [ ADAPTIVE &#124; STATIC ]                                                                                                                                                                                                                          | Convert to COMPRESS                                         | 
| 6   | [ INDEX &#124; LONG ] IN tablespace-name                                                                                                                                                                                                                                   | Keep only IN tablespace-name and convert to TABLESPACE tablespace-name | 
| [7](#7) | partition-expression: column-name [ NULLS LAST &#124; NULLS FIRST ]                                                                                                                                                                                                      | Keep only column-name                                       | 
| 8   | partition-element                                                                                                                                                                                                                                                   | Keep only PARTITION partition-name and partition-tablespace-options | 
| 9   | boundary-spec: [ starting-clause ] ending-clause                                                                                                                                                                                                                     | Keep only ending-clause and convert to VALUES LESS THEN     | 
| 10  | partition-tablespace-options: [ INDEX &#124; LONG ] IN tablespace-name                                                                                                                                                                                                    | Keep only IN tablespace-name and convert to TABLESPACE tablespace-name | 

## CREATE SEQUENCE

|No. |DB2 Syntax/Keywords |YashanDB23 Transformation Rules |
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| [11](#11) | AS INTEGER &#124; data_type                 | Remove                                   | 
| 12  | [ START WITH ( numeric-constant &#124; 1 ) ] [ INCREMENT BY ( numeric-constant &#124; 1 ) ] [ NO MAXVALUE &#124; MAXVALUE numeric-constant ] [ NO MINVALUE &#124; MINVALUE numeric-constant ] [ [ NO ] CYCLE ] [ NO CACHE &#124; CACHE numeric-constant ] [ [ NO ] ORDER ] | Keep all                              |

## Complete Examples

<span id="1" name="1" class="yaslink"></span>

### Example 1

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
 -- YashanDB23 Expected Result
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

### Example 2

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
-- YashanDB23 Expected Result
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

### Example 3

```sql
 -- DB2
CREATE SEQUENCE SEQ1 AS INTEGER
	MINVALUE 1 MAXVALUE 2147483647
	START WITH 1 INCREMENT BY 1
	CACHE 20 NO CYCLE NO ORDER;
-- YashanDB23 Expected Result
CREATE SEQUENCE SEQ1 INCREMENT BY 1 START WITH 1 MAXVALUE 2147483647 MINVALUE 1 CACHE 20 NOCYCLE  NOORDER;

```
