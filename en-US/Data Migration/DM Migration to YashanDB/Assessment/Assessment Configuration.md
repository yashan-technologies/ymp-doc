At this stage, you can configure the information required for the assessment task. After completion, choose to save the configuration or execute the assessment task immediately based on the configuration.

The configuration items for online assessment and offline assessment are not the same, please operate on the correct page.

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

Among all the listed objects, for DM8 source, the selectable objects include tables, constraints, indexes, views, sequence, materialized views, synonyms, triggers, UDT, UDF, packages, and stored procedures.

### Select Assessment Scope

This area will list the Schemas available for compatibility assessment in DM for user selection.

The system-level Schemas from the source side will not be displayed in the list and cannot be selected by the user.

If a Schema on the source side has the same name as a YashanDB system Schema, and the user selects that Schema, the configuration information cannot be saved, and the next step task cannot be executed.

If the assessment database is chosen as an external library, and there exists a Schema with the same name as the selected Schema in the external library, the system will prompt the user whether to overwrite.

### Advanced Options

This area is used to configure the following policies during the assessment process:

- VARCHAR length yesno in character units: The default value is no. This configuration must be consistent with the configuration of the source DM and can be queried via `SELECT NAME, VALUE, DESCRIPTION FROM v$parameter WHERE NAME IN ('LENGTH_IN_CHAR')`. VALUE of 0 is equivalent to **[ no ]**, and 1 is equivalent to **[ yes ]**.
  - Choosing **[ yes ]** means VARCHAR length is in character (CHAR) units.
  - Choosing **[ no ]** means VARCHAR length is in byte (BYTE) units.

- Whether to check invalid objects: The default value is yes.
  - Choosing **[ yes ]** will assess the objects in the source database that are currently in an invalid state.
  - Choosing **[ no ]** will not assess the objects in an invalid state.

- Manual DDL modification retention policy: The default value is full retention.
  - Full retention: All manually modified DDLs will be retained.
  - Retain unchanged only: Only retain manually modified DDLs that have not changed from the source.
  - Do not retain: No manually modified DDLs will be retained.

- Whether to enforce case sensitivity: The default value is no.
  - Yes: All object and field names will be treated in uppercase during assessment (except PL objects).
  - No: All object and field names will be assessed according to their actual case.



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


