This document is based on the existing PG syntax and has undergone rule-based transformation according to SQL conversion.

## Overview

During the task evaluation phase of YMP, a compatibility assessment of objects (tables, views, constraints, indexes, etc.) in the source database is performed to ensure that the source database objects can be successfully migrated to the target database. 
In the assessment phase, attempts will be made to obtain the DDL of the source database objects and execute it in the target database. If execution is successful, it indicates that this object is natively compatible and does not require special handling.  
Otherwise, the SQL conversion tool will use syntax conversion rules from the source database to the target database to perform syntactic transformations on this DDL to adapt to the syntax features of the target database.  
If the conversion is successful, an attempt will be made to execute the converted SQL in the target database. If execution is successful, it indicates that this object is automatically compatible and can be migrated.  
Since PG does not provide a system method for obtaining object DDL, the DDL in the assessment phase is assembled by the YMP platform using partial metadata, which may result in some unsupported syntax.

## CREATE TABLE

|No. |PG Syntax/Keyword |YashanDB23 Conversion Rules |
|---------|-------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------|
| [1](#1)  | [ [ GLOBAL &#124; LOCAL ] { TEMPORARY &#124; TEMP } &#124; UNLOGGED ]                                                           | Retain TEMPORARY, convert UNLOGGED to NOLOGGING             |
| 2      | column_name data_type [ COMPRESSION compression_method \] [ COLLATE collation ]                                         | Remove COLLATE information                                   |
| 3      | Table constraints                                                                                                           | Convert to independent constraint objects                   |
| 4      | GENERATED ALWAYS AS ( generation_expr ) STORED                                                                          | Retain only for YashanDB version >= 23.4.2                  |
| 5      | GENERATED { ALWAYS &#124; BY DEFAULT } AS IDENTITY [ ( sequence_options ) ]                                             | Change its default value to the default generated sequence name |
| 6      | PARTITION BY { RANGE &#124; LIST &#124; HASH } ( { column_name &#124; ( expression ) } [ COLLATE collation ] [ opclass ] [, ... ] ) | Retain only partition definitions supported by YashanDB; as YashanDB partition definitions require more information, most are incompatible |
| 7      | WITH ( storage_parameter [= value] [, ... ] )                                                                                  | Remove                                                     |
| 8      | INHERITS ( parent_table [, ... ] )                                                                                             | Do not obtain; switch to obtaining all field information    |
| 9      | TABLESPACE \<tablespace_name>                                                                                                   | Retain                                                     |

## CREATE SEQUENCE

|No. |PG Syntax/Keyword |YashanDB23 Conversion Rules |
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| [10](#10) | AS data_type                                                                                                                                                            | Remove                        |
| 11     | [ INCREMENT [ BY ] increment ]  [ MINVALUE minvalue &#124; NO MINVALUE ] [ MAXVALUE maxvalue &#124; NO MAXVALUE ] [ START [ WITH ] start ] [ CACHE cache ] [ [ NO ] CYCLE ] | Retain all; CACHE will not be retained if the value is 1     |
| 12     | OWNED BY { table_name.column_name &#124; NONE }                                                                                                                             | Do not obtain, do not retain  |

## CREATE INDEX

|No. |PG Syntax/Keyword |YashanDB23 Conversion Rules |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|----------------|
| 13     | [ UNIQUE ]                                                                                                                               | Retain                      |
| 14     | [ CONCURRENTLY ] [ [ IF NOT EXISTS ] name ]                                                                                              | Retain only name            |
| 15     | [ ONLY ]                                                                                                                                 | Do not obtain, do not retain |
| 16     | [ USING method ]                                                                                                                         | Do not retain               |
| 17     | [ COLLATE collation ] [ opclass [ ( opclass_parameter = value [, ... ] ) ] ] [ ASC &#124; DESC ] [ NULLS { FIRST &#124; LAST } ]                 | Retain only ASC/DESC        |
| [18](#18) | [ INCLUDE ( column_name [, ...] ) ] [ WITH ( storage_parameter [= value] [, ... ] ) ] [ TABLESPACE tablespace_name ] [ WHERE predicate ] | Retain only TABLESPACE      |

## CREATE VIEW

|No. |PG Syntax/Keyword |YashanDB23 Conversion Rules |
|-----------|-----------------------------------------------------|-----------------|
| 19     | [ OR REPLACE ] [ TEMP &#124; TEMPORARY ] [ RECURSIVE ]  | Do not retain                |
| 20     | [ WITH ( view_option_name [= view_option_value] ) ] | Retain only CHECK_OPTION      |
| [21](#21) | [ WITH [ CASCADED &#124; LOCAL ] CHECK OPTION ]         | Do not retain CASCADED/LOCAL |

## CREATE MATERIALIZED  VIEW

|No. |PG Syntax/Keyword |YashanDB23 Conversion Rules |
|----|-------------------------------------------------|----------------|
| 22  | WITH ( storage_parameter [= value] [, ... ] ) | Not obtained                  |
| 23  | USING method                                    | Not obtained                  |
| 24  | WITH [ NO ] DATA                               | Not obtained                  |
| 25  | TABLESPACE tablespace_name                      | Retain                      |

## All Examples

<span id="1" name="1" class="yaslink"></span>

### Example 1

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
 -- Expected result for YashanDB23
CREATE TABLE console_4 (
                           id INTEGER DEFAULT console_4_id_seq.NEXTVAL ,
                           name CLOB,
                           price NUMBER(10,2)
);
CREATE TABLE usbit2233( user_id CLOB
) NOLOGGING;

```

<span id="10" name="10" class="yaslink"></span>

### Example 2

```sql
 -- PG
CREATE SEQUENCE  drg.bedday_cof_dic_id_seq
    AS INTEGER
    INCREMENT BY 1 MINVALUE 1  MAXVALUE 2147483647
    START 1  CACHE 1  CYCLE;
-- Expected result for YashanDB23
CREATE SEQUENCE drg.bedday_cof_dic_id_seq INCREMENT BY 1 MINVALUE 1 MAXVALUE 2147483647 START WITH 1 CYCLE;
```

<span id="18" name="18" class="yaslink"></span>

### Example 3

```sql
 -- PG
CREATE INDEX idx_02 ON user_01.tb_01 
    USING btree (c1 NULLS FIRST, c2 DESC) 
    INCLUDE (c1, c2) 
    WHERE (c1 > 0)
-- Expected result FOR YashanDB23
CREATE INDEX idx_02 ON user_01.tb_01 (c1,c2 DESC);
```

<span id="21" name="21" class="yaslink"></span>

### Example 4

```sql
 -- PG
CREATE  VIEW user_01.view2  WITH (check_option=cascaded) AS  SELECT tb_01.c1,
    tb_01.c2,
    tb_01.c2_2
    FROM tb_01;
-- Expected result for YashanDB23
CREATE VIEW user_01.view2
AS
SELECT tb_01.c1, tb_01.c2, tb_01.c2_2
FROM tb_01
WITH CHECK OPTION;
```
