At this stage, you can configure the information required for the assessment task. Once completed, choose to save the configuration or execute the assessment task immediately based on the configuration.

The configuration items for online assessment and offline assessment are not the same. Please enter the correct page to operate.

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



### Select Assessment Objects

This area is used to select the objects for compatibility assessment.

Among all the listed objects, for the MySQL source side, the selectable objects include tables, constraints, indexes, views, triggers, UDF, stored procedures, and Events.

### Select Assessment Scope

This area will list the schemas available for compatibility assessment in MySQL, from which the user can select the scope.

The system-level schemas on the source side will not be displayed in the list, and users cannot select them.

If a schema on the source side has the same name as a YashanDB system schema, and the user selects that schema, the configuration information cannot be saved, and the next task cannot be executed.

If the assessment database is selected as an external library, and there exists a schema with the same name as the selected schema in the external library, the system will prompt the user to confirm whether to overwrite.

### Advanced Options

This area is used to configure the following strategies during the assessment process:

- Whether to be compatible with utf4mb format: The default value is No.
  - Selecting **[ yes ]** will map char(n) and varchar(n) columns with a character set of utf4mb to YashanDB's char(4n) and varchar(4n).
  - Selecting **[ no ]** means no processing will be done.
  
- Whether to check invalid objects: The default value is Yes.
  - Selecting **[ yes ]** will assess objects in the source database that are currently in an invalid state.
  - Selecting **[ no ]** will not assess objects in an invalid state.

- Whether conditional comments are effective: The default value is Yes. When the target side is YashanDB's MySQL mode, you can select whether conditional comments are effective.
  - Selecting **[ yes ]** will remove the comment symbols from MySQL special comments, keeping their content as SQL statement content.
  - Selecting **[ no ]** will keep the MySQL comments.
  
- Manual DDL modification retention policy: The default value is full retention.
  - Full retention: All manually modified DDLs will be retained.  
  - Retain only unchanged: Only retain manually modified DDLs that have not changed from the source side.  
  - No retention: Manually modified DDLs will not be retained at all.



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



### Upload Assessment File

For offline assessment, you need to upload the SQL statement file for execution during the assessment in this area.

The file content should ideally start with CREATE and ALTER; operations like DROP and DELETE are not recommended.

The file size should not exceed 200MB, and the following types of files are supported for upload:

- A single .sql file.
- A compressed file in zip or tar format.

After uploading the file, click **[ Next: Migration Assessment ]** to begin the assessment. If the file is large and is being split, terminating the assessment task may take some time.


