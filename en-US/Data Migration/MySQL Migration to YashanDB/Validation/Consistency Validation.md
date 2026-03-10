



The consistency check phase conducts a consistency check on specified table data between the source and target based on the verification initialization configuration. During the task execution process, users can view verification details and progress in real-time. After the verification is complete, users can view or download the verification logs and reports, and can download inconsistency results for the tables that are found to be inconsistent.

## Start Verification

When the verification task is initiated, the system will automatically check the verification objects and connection situation, including the following:

- YMP will check if the verification object exists. If it does not exist, the verification is deemed meaningless, and the task initiation will fail.
- Calculate the number of connections occupied by the migration task + the required connections for creating the verification task, and compare it with the maximum number of connections supported by the source database. If the former exceeds the latter, the task initiation will fail.
- Calculate the required connections for creating the verification task and the currently available connections provided by the source database. If the former exceeds the latter, the task initiation will fail.

If the above checks are satisfied, the verification task will start successfully, and the following interventions will be supported on the page:

- Terminate verification:If you need to terminate a task that is under verification, you can click **[Terminate]** in the operation column of the task list.

During the task execution, users can view verification details and progress in real-time, including:

* The task runtime is displayed with a minimum unit of time being minutes, rounding up if the actual time is not an integer.
* The task running log is continuously written to a specified path during execution, which can be viewed online after the task completes or downloaded as a zip file for storage.
* The verification details list will refresh in real-time. It will always display the source schema, source table, target schema, and target table. Other fields will be generated during the verification process.
* Users can select any table in the verification details list for **[ Table-Level Configuration ]**.
* The verification report is generated at the final stage of the task after all tables have been verified. It can be viewed online through the page or downloaded for saving.

## Verification Completion

The following is the information listed on the page after the verification is completed.

### Basic Information

Basic information includes task name, task type, source database information, target database information, task start time, and task end time.



### Verification progress

There are slight differences in the verification progress information displayed by full verification, primary key verification, and statistical verification:

:::tabs

== Full Validation

Display the specific progress of verification, including the total number of tables, completed verifications, total number of failed verifications, verifications in progress, unverified items, and their proportions.

== Primary key verification

Display the specific progress of verification, including the total number of tables, completed verifications, total number of failed verifications, verifications in progress, unverified items, and their proportions.

== Statistical Validation

Display the specific progress of verification, including the total number of tables, the number of completed verifications, the total number of failed verifications, and their proportions.

:::

### Validation Overview

There are slight differences in the verification overview information displayed by full verification, primary key verification, and statistical verification:

:::tabs

== Full Validation

Display the specific overview of verification, including the total number of tables, the total number of consistent tables, the total number of inconsistent tables, the total number of skipped tables, and their proportions.

The data types supported by full validation and their mapping relationships can be found in the [Full Validation Data Type Explanation](../../../Refrence/Full validation Data Type Explanation) document.

== Primary key verification

Display the specific overview of verification, including the total number of tables, the total number of consistent tables, the total number of inconsistent tables, the total number of skipped tables, and their proportions.

== Statistical Validation

Display the specific overview of verification, including the total number of tables, the total number of consistent tables, the total number of inconsistent tables, and their proportions.

:::

When there is data inconsistency, it is recommended to handle it according to the following steps:

1. **Analyze the cause of inconsistency**: Check if it is caused by data type conversion, character set encoding, or time zone settings.
2. **Focus on key business tables**: Prioritize resolving inconsistencies in core business tables.
3. **Selective repair**: For key data inconsistencies, resolve them through manual SQL or re-migrating specific tables.
4. **Record and assess the impact**: For non-key data differences, record and assess their impact on the business.

### Table Inconsistency Types Statistics

There are slight differences in the types of discrepancies of inconsistent tables displayed by full verification, primary key verification, and statistical verification:

:::tabs

== Full Validation

* Source table does not exist or target table does not exist: This type is determined when the source table or target table cannot be queried in the system view.
* This type is determined when there is a discrepancy in the number of data rows between the source table and the target table, or when there are inconsistencies in column contents within the rows corresponding to the primary key/unique key.
* Structural inconsistency: If the number of columns is inconsistent, or the types of corresponding columns in the source table and the target table do not match, it is determined to be of this type.

For tables that exist on both the source and target sides, full validation first checks whether primary key constraints and unique constraints are consistent, while for tables without primary keys, it only checks whether the number of rows is consistent.

== Primary key verification

* Source table does not exist or target table does not exist: This type is determined when the source table or target table cannot be queried in the system view.
* Data inconsistency: This type is determined when there are inconsistencies in column contents among rows corresponding to the primary key/unique key.
* Inconsistent structure: If the columns where the primary key/unique key are located are the same on the source and target sides, but the data types are inconsistent, it is judged as this type.

== Statistical Validation

* Source table does not exist or target table does not exist: This type is determined when the source table or target table cannot be queried in the system view.
* Data inconsistency: This type is determined when there is an inconsistency in the number of data rows between the source table and the target table.

:::

### Validation Details

It displays the validation information for each pair of source and target tables, including the source schema name, source table name, target schema name, target table name, check results, inconsistency types, check status, validation row count, validation duration, and result viewing. There are slight differences in the verification information displayed by full verification, primary key verification, and statistical verification:

:::tabs

== Full Validation

* **[ validation result ]**: Includes consistent, inconsistent, validation failed, skipped. Among these, structures that are currently unsupported validation will be skipped by default.
* **[ Inconsistent ]**: Includes data inconsistency, source table does not exist, target table does not exist, structural inconsistency, etc. If the validation result is consistent, this column will be empty.
* **[ Status ]**: Includes validation completed, validation exception, validation terminated, etc.
* **[ Validation Row ]** and **[ Duration ]**: Support clicking the sorting icon to sort the list by these columns.
* You can re-validate by single table or conditionally filtering tables.
* Click **[ download inconsistent data ]** to download the validation results; the download file type is CSV.
* Click **[ Details ]** for each table row to view the validation results of this table in the source and target side:
  * There are five types of inconsistency results: redundancy, absence, difference, illegal values, and unique key being NULL.
  * Other than primary key differences, the results of other inconsistency types will display **[ First Non-Matching Column Name ]**.
  * When the types of inconsistencies are missing values, redundancies, and discrepancies, the inconsistent primary key columns and their corresponding associated inconsistent data items are uniformly converted to character type.

== Primary key verification

* **[ validation result ]**: Includes consistent, inconsistent, validation failed, skipped. Among these, structures that are currently unsupported validation will be skipped by default.
* **[ Inconsistent ]**: Includes data inconsistency, source table does not exist, target table does not exist, structural inconsistency, etc. If the validation result is consistent, this column will be empty.
* **[ Status ]**: Includes validation completed, validation exception, validation terminated, etc.
* **[ Validation Row ]** and **[ Duration ]**: Support clicking the sorting icon to sort the list by these columns.
* You can re-validate by single table or conditionally filtering tables.
* Click **[ download inconsistent data ]** to download the validation results; the download file type is CSV.
* Click **[ Details ]** for each table row to view the validation results of this table in the source and target side:
  * There are five types of inconsistency results: redundancy, absence, difference, illegal values, and unique key being NULL.
  * When the types of inconsistencies are missing values and redundancies, the inconsistent primary key columns and their corresponding associated inconsistent data items are uniformly converted to character type.

== Statistical Validation

* **[ validation result ]**: Includes consistent, inconsistent, and validation failed.
* **[ Inconsistent ]**: Includes data inconsistency, source table does not exist, target table does not exist, etc. If the validation result is consistent, this column will be empty.
* **[ Status ]**: Includes validation completed, validation exception, validation terminated, etc.
* **[ Validation Row ]** and **[ Duration ]**: Support clicking the sorting icon to sort the list by these columns.
* You can re-validate by single table or conditionally filtering tables.
* Click **[ Details ]** for each table row to view the validation results of this table in the source and target side:
  * When the validation result is consistent, or inconsistent due to data inconsistency, it will display the source data row count, target data row count, and the validation result.
  * When the validation result is inconsistent due to the non-existence of the table, it will directly show the inconsistency in the validation result.

:::


