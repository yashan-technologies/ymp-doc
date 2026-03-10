At this stage, you can configure the information required for the evaluation task. Once completed, select Save Configuration, or execute the evaluation task immediately based on the configuration.

The configuration items for online evaluation and offline evaluation are not the same. Please navigate to the correct page for operation.

## Online Evaluation Configuration



### Basic Information

The **[Basic Information]** section displays the version, connection, and other information of the source and target databases. It supports editing, and when saving, the system will check whether the Source Database connection user meets the Privilege requirements for the selected task steps. If any Privileges are missing, a prompt will appear.

### Selecting Assessment Database

The assessment requires executing the apply on some YashanDB to verify compatibility.

This area allows users to choose the built-in YashanDB for the assessment (the YMP deployed using the Install command will come with a YashanDB as the built-in database); or, if the built-in database does not meet the requirements, select a YashanDB that has been deployed by the user.

For example, if the version of the built-in database provided by YMP does not match the target database version for migration, it can be considered that the built-in database does not meet the requirements, and the system will pop up a message suggesting that the user selects an external database.

- Built-in database: Default value. If the default table type is LSC, YMP will automatically mount DATABUCKET under system and user tablespaces.
- External database: Please ensure that the assessment database matches the target database version for migration, otherwise the assessment results may be inaccurate, leading to migration unavailability. The database user must have DBA privilege.

When the assessment database is in Yashan mode, the system will automatically create a Schema with the same name as the source during the assessment; when in MySQL mode, in addition to creating the Schema, it will also create a Database with the same name. If the user used for the assessment is not in the Database list, compatibility inconsistency results may occur due to user non-existence.



### Select Evaluation Objects

This area is used to select objects for compatibility evaluation.

Among all listed objects, for PostgreSQL source, the selectable objects include tables, constraints, indexes, views, sequences, materialized views, UDTs, triggers, UDFs, and stored procedures.

### Select Evaluation Scope

This area will list the schemas available for compatibility evaluation in PostgreSQL, from which users can select the scope.

Note that system-level schemas on the source side will not be displayed in the list, and users cannot select them.

If a schema on the source side has the same name as the YashanDB system schema and the user selects that schema, then the configuration information cannot be saved, and the next task cannot be executed.

If the assessment database is selected as an external library and there exists a schema with the same name as the selected schema in the external library, the system will prompt the user whether to overwrite.

### Advanced Options

This area is used to configure the following strategies during the evaluation process:

- Manual DDL Modification Retention Policy: The default value is Full Retention.
  - Full Retention: All manually modified DDL will be retained.

  - Retain Only Unchanged: Only the manually modified DDL that has not changed from the source DDL will be retained.

  - No Retention: No manually modified DDL will be retained.
