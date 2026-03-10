## Version Information

**Version Number**: 23.5.1

**Release Date**: November 7, 2025

**Version Upgrade**: Versions v23.1.1.0 and later can be directly upgraded to this version.

## Version Positioning

The Yashan Migration Platform (YMP) is a data migration product provided to support the Yashan database. Version 23.5 enhances the synchronization functionality between MySQL and YashanDB, enriches the supported scenarios, and supports integration with DB2. It significantly improves the availability of data migration, enriches the data source side, enhances product usability, and enables low-threshold, low-cost, and high-efficiency heterogeneous database migration implementation. The support for data sources such as MySQL, PostgreSQL, DB2, and Oracle has been strengthened, improving migration stability and flexibility, and providing more refined verification capabilities to help users achieve efficient and accurate data migration.

## Feature Updates

### New DB2 Data Source

YMP has added support for the DB2 data source type, enabling data migration scenarios with DB2 as the source, with the following capabilities:

* Supports DB2 versions 9.5 and 10.1.
* Supports source evaluation, metadata migration, data migration, and statistical verification for DB2. Supports metadata synchronization, including tables, primary keys, etc.

### Support for New Data Source Versions

* Oracle Data Source:
  * Supports Oracle 10g for source evaluation, metadata migration, data migration, and verification.
* PostgreSQL Data Source:
  * Supports PostgreSQL 9.6 for source evaluation, metadata migration, data migration, and verification.

### Increased Migration Compatibility

YMP V23.4 has completed the following optimizations for migration scenarios with Oracle, YashanDB, and MySQL as source sides:

* Oracle as source:
  * Supports automatic filtering of incompatible privilege statements during source evaluation.
  * Supports initialization of partial allocation parameters when Oracle is the source.
  * Supports periodic offline verification for Oracle.
  * Supports compatibility for partial syntax differences and function conversion during evaluation from Oracle to YashanDB.
  * Supports configuration for character set expansion from Oracle source GBK character set to YashanDB UTF-8 character set during evaluation.
  * Supports automatic optimization of certain export parameters during Oracle data migration.
  * Supports evaluation, migration, and verification of Oracle's ROWID type.
  * Supports evaluation and migration of BFILE type field data (excluding file data).
  * Supports compatibility with Oracle 9i version, supporting the complete process of metadata evaluation, data migration, and verification.
* YashanDB as source:
  * Configures three ways to split ROWID for large tables.
* MySQL as source:
  * Supports evaluation + metadata migration with SQL_MODE containing ANSI_QUOTES when MySQL is the source.
  * Supports setting conditions for whether conditional comments take effect during evaluation from MySQL to YashanDB (MySQL mode).
* PostgreSQL as source:
  * Supports compatibility evaluation of objects under the PUBLIC user in PostgreSQL.
  * Supports renaming of SCHEMA during migration with PostgreSQL as the source.

### Performance Optimization

* Oracle as source:
  * Optimizes data migration performance from Oracle to YashanDB for partition tables and LOB tables.

### Data Validation Capabilities

* Verification configuration supports user-defined configurations on the page, including performance configurations, zero-value verification configurations, null-value verification configurations, etc.
* Supports conditional verification, allowing users to write WHERE condition expressions for both source and target databases separately. The platform will filter and validate table data that meets the criteria. This functionality can be used for incremental data validation scenarios, where users need not initiate full validations each time, enhancing verification speed.
* Supports users to set verification strategies that automatically initiate verification tasks periodically and generate verification reports.

### Usability Improvements

* Optimizes the display and interaction prompts for migrating from MySQL to YashanDB (MySQL mode).
* Optimizes the error message line number for TRIGGER type evaluation.
* Optimizes the evaluation performance of long DDL for constraints USING INDEX on partition tables.
* Optimizes the independent dependencies of various internal tools.
* Optimizes the display of advanced migration configuration interface and adds functionality for confirmation, cancellation, and restoring default values.
* Optimizes the control and display of pre-check items for migration configuration.
* Supports retaining some comments in DDL during evaluation conversion.
* Supports automatically adding YMP import tool dependencies during deployment.
* Supports independent display of time spent on metadata migration and data migration.
* Supports configuring migration task running parameters through the interface.
* Supports flexible width adjustment for columns in offline reports.
* Supports configuration for browsers not to save passwords.
* Supports displaying details of object evaluation during evaluation.
* Supports displaying and initializing tablespaces during data source migration for ordinary users.
* Supports setting WHERE filtering conditions during data migration.
* Supports displaying the maximum migration speed during data migration.
* Supports displaying character sets for Oracle, YashanDB, and DM data sources.
* Supports displaying verification progress, including total number of tables, number of tables being verified, number of completed verifications, and number of failed verifications.

## Supported Migration Products and Versions

|Capability Module |Data Source |Data Source Version |Data Source Deployment Type |Target Side |Target Version |
|------|--------------------|--------------------|-----------|----------------------------|---------------------|
| Data migration | Oracle             | 9i, 10g, 11g, 12c, 19c, 21c | Standalone primary/standby, YAC | YashanDB (Yashan mode)         | 22.2, 23.2, 23.3, 23.4 |
|      | MySQL              | 5.6, 5.7, 8.0        | Standalone primary/standby, Cluster   | YashanDB (Yashan mode + MySQL mode) | 22.2, 23.2, 23.3, 23.4 |
|      | DM                 | 8.1                | Standalone primary/standby, Cluster   | YashanDB (Yashan mode)         | 22.2, 23.2, 23.3, 23.4 |
|      | DB2                 | 9.5, 10.1             | Standalone   | YashanDB (Yashan mode)         | 22.2, 23.2, 23.3, 23.4 |
|      | YashanDB (Yashan mode) | Versions 23.2.4 and later      | Standalone primary/standby      | YashanDB (Yashan mode)         | Versions 23.2.4 and later       |
|      | PostgreSQL         | 9.6, 14.x, 15.x          | Standalone        | YashanDB                   | 23.2, 23.3, 23.4     |
| Real-time synchronization | YashanDB           | 23.2.7, 23.3, 23.4  | Standalone, YAC    | Oracle                     | 19c                 |
|      | YashanDB           | 23.3, 23.4          | Standalone, YAC    | YashanDB                   | 23.3, 23.4           |
|      | YashanDB           | 23.3, 23.4          | Standalone, YAC    | MySQL                      | 5.7                 |
|      | MySQL              | 5.7                | Standalone primary/standby, Cluster   | YashanDB                   | 23.3, 23.4           |

## Version Compatibility

The YashanDB migration platform (v23.5) is compatible with the YashanDB database versions v22.2, v23.1, v23.2, v23.3, v23.4, v23.5, supporting both standalone and YAC deployment forms.

## Version Constraints

|Capability Module |Constraint Description |
| --- |-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Evaluation Migration | * Modifying the user's password during evaluation requires quotes ""; otherwise, renaming SCHEMA mapping during migration will report an error.<br/>* Currently does not support using YashanDB database with three rights separation for evaluation library.<br/>* When evaluating DB2 as the source, if the object name contains special symbols such as ' that are restricted by the DB2LOOK tool, it may interrupt the exporting DDL and the evaluation task will fail.<br/>* When evaluating PG as the source, tables with multiple generated auto-increment properties cannot be automatically compatible.<br/>* PG UDT currently only supports composite types, RANGE, and ENUM.<br/>* GIS types currently only support assessment using external libraries, which need to be specified as a plugin package when installing the YashanDB database. Additionally, this plugin package cannot be independently installed after the YashanDB server installation is completed.<br/>* For adding Oracle 9i and 10g data sources, the connection user's password cannot exceed 8 characters.<br/>* When evaluating Oracle 9i and 10g as the source, the evaluation range will display some system users.<br/>* When migrating from Oracle 9i and 10g as the source, the pre-check does not check for active scheduled tasks on the source side.<br/>* When evaluating Oracle 9i and 10g as the source, the check for constraints (including non-null constraints) may lose some attributes.<br/>* For migrating users and privileges from Oracle 9i, Oracle 10g, and Oracle 11g as the source, encountering unsupported advanced package authorization statements will report errors, and the user will be evaluated as incompatible.            |
| Data Migration | * Currently, data migration for BFILE is not supported for Oracle 9i and Oracle 10g.<br/>* DM and MySQL large object type empty_lob data migration will result in NULL data in YashanDB.<br/>* Direct migration of data with the GENERATED ALWAYS attribute list is not supported from YashanDB to YashanDB.<br/>* Multi-character emoji migration is not supported.<br/>* When using DB2 as the source, migration of large object types exceeding 1023M is not supported.<br/>* Migration of GIS type data exceeding 1023M is not supported.<br/>* When using DB2 as the source for data migration, the DB2 client must be manually deployed first.<br/>* When using DB2 as the source for data migration, some special emojis may not migrate correctly, resulting in differences in byte counts compared to the source.<br/>* GIS types are not supported for migration to YashanDB version 23.3.<br/>* PG and YashanDB as sources do not support migration of GIS types exceeding 2M.        |
| Real-time Synchronization| * The metadata stage supports constant default values, while non-constant default values will still be synchronized during the metadata stage but without any special handling. If synchronization fails, the user needs to intervene for corrections and rerun the task.<br/>* In the incremental data synchronization stage, DDL only supports constant default values, values that are `null`, `empty_clob()`, and `empty_blob()`. Other types of default values will cause the task to fail.<br/>* When YashanDB is the source, DDL cannot be executed on objects within the synchronization scope during the metadata synchronization and full data synchronization stages, otherwise synchronization will fail. |
| Data Validation | * Various sources do not support validation of primary key (or unique key) column data containing #, ", `, \n, /, /n, emojis, English commas, and other special characters.<br/>* Currently, validation between MySQL and YashanDB (mysql mode) for time types beyond the Yashan range is not supported.<br/>* PG does not support specifying collate column sorting when creating tables, e.g., create table tb_01(c1 char(10) collate "en_US.utf8" unique), c1 column sorting may be incorrect, resulting in verification inconsistency.<br/>* When using Oracle as a data source, the data types that do not support full validation include: JSON, UDT.<br/>* When using DM as a data source, the data types that do not support full validation include: IMAGE, UDT.<br/>* Data exceeding the numerical range of YashanDB can be successfully migrated (e.g., boundary values of Float types from Oracle and DM turning into infinity in YashanDB), but the verification results may indicate inconsistencies.<br/>* Unreadable strings are not supported for data validation.<br/>* Validation does not support data in CLOB/NCLOB containing emoji-type symbols.<br/>* Validation in MySQL mode does not support comparisons between unsigned and signed numbers.|

## Issues Fixed

1. Fixed the issue with warning log missing view when using Oracle 10g as the source.
2. Fixed the issue of mismatched number of called parameters during stored procedure evaluation conversion.
3. Fixed the issue of primary key constraint creation failure on tables.
4. Fixed the issue of confusion in obtaining DDL for DB2 objects with partially similar names during evaluation.
5. Fixed the issue of sequence starting values not meeting expectations during evaluation with PG as the source.
6. Fixed the issue of incorrect total data amount displayed during DB2 migration.
7. Fixed the issue of MySQL's TEXT type empty strings being migrated as NULL violating constraints.
8. Fixed the issue of DB2 connection count not meeting verification error requirements.
9. Fixed the issue of losing the conditions after the when statement of triggers after evaluation.
10. Fixed the issue of DB2 multiple indexes on the same table experiencing prolonged evaluation stalling.