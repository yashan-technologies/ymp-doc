At this stage, you can configure the information needed for the assessment task. Once completed, choose to save the configuration, or execute the assessment task immediately based on the configuration.



### Basic Information

The **[Basic Information]** section displays the version, connection, and other information of the source and target databases. It supports editing, and when saving, the system will check whether the Source Database connection user meets the Privilege requirements for the selected task steps. If any Privileges are missing, a prompt will appear.

### Selecting Assessment Database

The assessment requires executing the apply on some YashanDB to verify compatibility.

This area allows users to choose the built-in YashanDB for the assessment (the YMP deployed using the Install command will come with a YashanDB as the built-in database); or, if the built-in database does not meet the requirements, select a YashanDB that has been deployed by the user.

For example, if the version of the built-in database provided by YMP does not match the target database version for migration, it can be considered that the built-in database does not meet the requirements, and the system will pop up a message suggesting that the user selects an external database.

- Built-in database: Default value. If the default table type is LSC, YMP will automatically mount DATABUCKET under system and user tablespaces.
- External database: Please ensure that the assessment database matches the target database version for migration, otherwise the assessment results may be inaccurate, leading to migration unavailability. The database user must have DBA privilege.

When the assessment database is in Yashan mode, the system will automatically create a Schema with the same name as the source during the assessment; when in MySQL mode, in addition to creating the Schema, it will also create a Database with the same name. If the user used for the assessment is not in the Database list, compatibility inconsistency results may occur due to user non-existence.



### Selecting Assessment Objects

This area is used to select the objects for compatibility assessment.

Among all the listed objects, for the DB2 source, the selectable objects include tables, constraints, indexes, views, sequences, UDFs, and stored procedures.

### Selecting Assessment Scope

This area will list the Schemas available in DB2 for compatibility assessment, from which the user can select.

System-level Schemas from the source side will not be displayed in the list, and the user cannot select them.

If a Schema on the source side has the same name as the YashanDB system Schema and the user selects that Schema, the configuration information cannot be saved, and the next task cannot be executed.

If the assessment database is selected as an external database, and a Schema with the same name as the selected Schema exists in the external database, the system will prompt the user whether to overwrite it.

### Advanced Options

This area is for configuring the retention policy for manually modified DDLs during the assessment process:

- Fully retained: All manually modified DDLs will be retained.
- Only retain unchanged: Only retain manually modified DDLs that have not changed from the source.
- Not retained: No manually modified DDLs will be retained.

The default value is fully retained.
