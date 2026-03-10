At this stage, you can configure the information required for the assessment task. Once completed, select Save Configuration, or execute the assessment task immediately based on the configuration.

The configuration items for online assessment and offline assessment are not the same; please go to the correct page for operation.

## Online Assessment Configuration



### Basic Information

The **[Basic Information]** section displays the version, connection, and other information of the source and target databases. It supports editing, and when saving, the system will check whether the Source Database connection user meets the Privilege requirements for the selected task steps. If any Privileges are missing, a prompt will appear.

### Selecting Assessment Database

The assessment requires executing the apply on some YashanDB to verify compatibility.

This area allows users to choose the built-in YashanDB for the assessment (the YMP deployed using the Install command will come with a YashanDB as the built-in database); or, if the built-in database does not meet the requirements, select a YashanDB that has been deployed by the user.

For example, if the version of the built-in database provided by YMP does not match the target database version for migration, it can be considered that the built-in database does not meet the requirements, and the system will pop up a message suggesting that the user selects an external database.

- Built-in database: Default value. If the default table type is LSC, YMP will automatically mount DATABUCKET under system and user tablespaces.
- External database: Please ensure that the assessment database matches the target database version for migration, otherwise the assessment results may be inaccurate, leading to migration unavailability. The database user must have DBA privilege.

When the assessment database is in Yashan mode, the system will automatically create a Schema with the same name as the source during the assessment; when in MySQL mode, in addition to creating the Schema, it will also create a Database with the same name. If the user used for the assessment is not in the Database list, compatibility inconsistency results may occur due to user non-existence.



### Select Assessment Object

This area is used to select objects for compatibility assessment.

Among all listed objects, for Oracle source, the selectable objects include users, privileges, tables, constraints, indexes, views, sequences, materialized views, synonyms, triggers, UDTs, UDFs, packages, and stored procedures.

If a synonym is chosen here, during data migration, the system will automatically migrate all associated public synonyms within the selected schema range, and the schema of these synonyms will be set to PUBLIC.

### Select Assessment Scope

This area will list the schemas in Oracle that can be used for compatibility assessment and allow the user to select the scope.

System-level schemas on the source side will not be displayed in the list and cannot be selected by the user.

If a schema on the source side has the same name as a YashanDB system schema and the user selects that schema, the configuration information cannot be saved, and the next task cannot be executed.

If the assessment database is chosen as an external library and there exists a schema with the same name as the selected schema in the external library, the system will prompt the user whether to overwrite.

### Advanced Options

This area is used to configure the following policies during the assessment process:

- Whether to check invalid objects: The default value is Yes.
  - Selecting **[ yes ]** will assess objects in the source database that are currently in an invalid state.
  - Selecting **[ no ]** will not assess objects in an invalid state.

- Whether to ignore nologging: The default value is Yes.

  - Selecting **[ yes ]** will remove the nologging keyword from tables and indexes during compatibility assessment.
  - Selecting **[ no ]** will not perform any processing.

  YashanDB requires the NOLOGGING state on tables and indexes to be consistent; otherwise, an error will be thrown during assessment or migration. Successful operations will only occur after removing the NOLOGGING keyword.

- Assessment database deployment mode: The default value is standalone. When selecting cluster deployment mode, tables and materialized views with subpartitions will be considered incompatible for assessment.

- Whether to extend character length for different character sets: The default value is No. When the source character set is GBK and the target character set is UTF8, you can choose whether to automatically extend character type length.

  - Selecting **[ yes ]** will extend character type lengths during assessment, converting char(n), varchar(n) to char(n char), varchar(n char).
  - Selecting **[ no ]** will not perform any processing.

- Manual modification of DDL retention policy: The default value is Full retention.
  - Full retention: All manually modified DDLs will be retained.

  - Retain only unmodified: Only retain manually modified DDLs that have not changed on the source side.

  - Do not retain: Do not retain any manually modified DDLs.

- Whether to retain incompatible constraints USING INDEX: The default value is No.

  - Selecting **[ yes ]** will retain the USING INDEX attribute, which may be incompatible with some constraints.
  - Selecting **[ no ]** will not retain the USING INDEX attribute, and the existing index on this column will be used by default.

- Partition index transition to LOCAL length: Range item, value [-1, 1000000000000].

  - The DDL of partition indexes exceeding the configured length will remove the partition storage properties, including partition tablespace information, only retaining the LOCAL keyword. -1 indicates no length limit.

## Offline Assessment Configuration



### Basic Information

The **[Basic Information]** section displays the version, connection, and other information of the source and target databases. It supports editing, and when saving, the system will check whether the Source Database connection user meets the Privilege requirements for the selected task steps. If any Privileges are missing, a prompt will appear.

### Selecting Assessment Database

The assessment requires executing the apply on some YashanDB to verify compatibility.

This area allows users to choose the built-in YashanDB for the assessment (the YMP deployed using the Install command will come with a YashanDB as the built-in database); or, if the built-in database does not meet the requirements, select a YashanDB that has been deployed by the user.

For example, if the version of the built-in database provided by YMP does not match the target database version for migration, it can be considered that the built-in database does not meet the requirements, and the system will pop up a message suggesting that the user selects an external database.

- Built-in database: Default value. If the default table type is LSC, YMP will automatically mount DATABUCKET under system and user tablespaces.
- External database: Please ensure that the assessment database matches the target database version for migration, otherwise the assessment results may be inaccurate, leading to migration unavailability. The database user must have DBA privilege.

When the assessment database is in Yashan mode, the system will automatically create a Schema with the same name as the source during the assessment; when in MySQL mode, in addition to creating the Schema, it will also create a Database with the same name. If the user used for the assessment is not in the Database list, compatibility inconsistency results may occur due to user non-existence.



### Upload Assessment Files

For offline assessment, you need to upload SQL statement files for execution in the assessment.

The content of the files should ideally start with CREATE and ALTER; operations like DROP, DELETE are not recommended.

File size must not exceed 200MB, supporting the following file types:

- A single .sql file.
- A single .xml file: must be in MyBatis-supported Mapper XML format.
  - Only DML statements are supported; DDL statements that may exist in the \<update> tag will be deemed incompatible.
  - For the dynamic SQL tag \<choose> in MyBatis, parsing can only concatenate the SQL content of the first \<when> clause.
  - Parameters in external references using the \<include> tag's \<property> parameter will no longer be passed automatically. See [Common Questions about Assessment](../../../FAQ/Assessment Task).
  - During compatibility assessment, YMP will automatically generate corresponding schema and table information based on the SQL statements in the files. If the generated result tables differ significantly from the tables actually used by the application, the user can manually modify the target SQL and re-validate or refresh the report completely. See [Common Questions about Assessment](../../../FAQ/Assessment Task).

- A compressed file in zip or tar format: can only contain one type of supported file format: all .sql or all .xml.

After uploading files, click **[ Next: Migration Assessment ]** to proceed with the assessment. If the files are large and in the process of being split, terminating the assessment task may take some time.
