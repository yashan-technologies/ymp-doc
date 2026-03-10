

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

Display the specific progress of verification, including the total number of tables, the number of completed verifications, the total number of failed verifications, and their proportions.

### Verification Overview

Display the specific overview of verification, including the total number of tables, the total number of consistent tables, the total number of inconsistent tables, and their proportions.

When there are data inconsistencies, it is recommended to handle them as follows:

1. **Analyze the cause of inconsistency**: Check if it is caused by data type conversion, character set encoding, or timezone settings.
2. **Focus on key business tables**: Prioritize resolving inconsistencies in core business tables.
3. **Selective repair**: For critical data inconsistencies, resolve them with manual SQL or by re-migrating specific tables.
4. **Record and evaluate the impact**: For non-critical data differences, record and evaluate their impact on the business.

### Inconsistent Table Type Statistics

Displays the types of differences for inconsistent tables, including:

* Source table does not exist or target table does not exist: This type is determined when the source or target table cannot be queried in the system view.
* Data inconsistency: This type is determined when there is a discrepancy in the number of data rows between the source and target tables.

### Verification Details

Displays the verification information for each pair of source and target tables, including source schema name, source table name, target schema name, target table name, check results, inconsistency type, check status, number of validated rows, duration of verification, and result viewing options:

* **[validation result]**: Includes three values: consistent, inconsistent, and verification failed.
* **[ Inconsistent ]**: Includes values such as data inconsistency, source table does not exist, target table does not exist, etc. If the verification result is consistent, this column will be empty.
* **[ Status ]**: Includes status values such as verification completed, verification exception, and verification terminated.
* **[ Validation Row ]** and **[ Duration ]**: Support sorting by clicking on the sorting icon for these columns.
* Enables re-verification via single-table or condition filtering.
* Click on **[ Details ]** for each table row to view the verification results for that table between the source and target:
  * When the verification result is consistent, or inconsistent due to data inconsistency, the number of data rows from the source and target along with the verification result will be displayed.
  * Inconsistent results due to table non-existence will directly display the verification result as inconsistent.
