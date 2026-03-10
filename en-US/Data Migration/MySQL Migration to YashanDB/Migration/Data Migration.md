The data migration phase mainly implements the migration of metadata and data. Before migration, multiple checks will be performed. Once the checks pass, migration will begin, and users can view the migration progress and details in real-time.

The overall rules for YMP's data migration are as follows:

- Supports pausing migration tasks during execution, resuming migration after pausing, terminating migration after pausing, and displaying current progress information after pausing.
- For users (Schemas) newly created on the target side by the migration task, these users (Schemas) will be granted both CONNECT and RESOURCE privileges.

- Once migration is complete, the default password for the DBLINK object will be set to yasdb_123.

  - If the source side contains discarded rows that lead to migration failure, the data file will not be retained. Detailed information on discarded rows migration failure can be found in the discard.log file located in the same directory as the data file (YMP installation directory/tmp/).

  - If the schema used by the migration task in the starting step is already used by other tasks in the selected target database, a prompt will appear when saving or proceeding to the next step stating that [xxxx] is already being used in task [yyyy] on the target side. Here, xxxx is the name of the affected schema, and yyyy is the name of the affected task.
  - All column names in the target database table must be included in the source database table, and the names on the target side must be uppercase; otherwise, migration will fail.

  - Migration for schema names or table names containing single quotes is not supported.
  - Migration tasks that do not start from the assessment phase do not support migration if there are table objects with the same name (case insensitive).



## Start Migration

After starting the migration task, the page supports the following intervention operations:

- **[ Paused ]**: The task is in data migration and can be paused. When the task is in the metadata migration phase, the pause operation typically completes within 10 seconds. If the pause operation is performed during tablespace creation and index creation, it may take longer. For tasks with a data conflict strategy of **[ no truncate ]**, they cannot be paused during the data migration phase.
- **[ terminate migration ]**: The task is in data migration and can be terminated. When the task is in the metadata migration phase, the termination operation generally completes within 10 seconds. If the termination operation is performed during tablespace creation and index creation, it may take longer.
- **[ migration recovery ]**: Recover a paused task.

## Migration Monitoring

During the migration task execution, the page will display various information in real time. It is recommended to focus on the following metrics:

- **Transfer Rate**: Real-time data transfer speed, usually measured in rows/second or MB/second.
- **Resource Usage**: CPU, memory, and I/O usage of both the source and target databases.
- **Error Log**: Real-time monitoring of the error log to address exceptional situations in a timely manner.

If the migration speed is abnormally slow, check:

- Whether network bandwidth is limited.
- Whether there are resource bottlenecks in the source or target database.
- Whether there are large transactions or large table migrations causing delays.
- Whether resources in the YMP deployment environment are sufficient: CPU, memory, disk I/O, etc.

### Basic Information

Displays basic information about the task. Before resuming or retrying the migration, users can click to modify the migration configuration.

### Overall Situation

Displays the progress of metadata migration, data migration, task runtime, task logs, migration reports, and performance monitoring data.

- For tasks with WHERE condition filtering for data migration, the data migration progress will not be displayed by default, only the amount of data migrated will be shown in real time.

- Provides the **[ Total data size after query filtering ]** operation to manually confirm the accurate progress after querying the WHERE condition.

- Before the data volume after completing condition filtering is queried, the object's **[ Migration Data ]** will be the amount of data successfully migrated in real time, and the successful data proportion will not be displayed.

### Object List

Displays the specific details of the migration objects: categorized according to object type.

> **Note**：
>
> The fault tolerance row count represents the real-time migration fault tolerance situation. When migration retries occur, the fault tolerance row count will be reset.

### View Details

Displays the specific DDL information and error information of the migration objects.

- Click **[Details]** to check the specific DDL information and error information of the migration objects. For completed migration tasks where the object conflict strategy is "overwrite", you can directly rewrite the corresponding object DDL and click **[Save]** to save the changes.
- After saving the modifications, the object will be regarded as being in a state where it has been modified but not re-migrated (there will be a small red dot at the "View Details" section for identification), and the object will be retried during migration retries.

### Bulk Modification

This area is used for bulk modification of sequence starting values:

* Users can select the sequence objects to modify and change the starting values of sequences in the target database's DDL.
* Users can set the increment for the sequence starting value, increasing it based on the original sequence starting value.
* The range for the increment of the sequence starting value must be greater than or equal to 0, and the modified sequence starting value cannot exceed the sequence maximum value (maxValue) or upper limit (9999999999999999999999999999).
* Users can click save and override to migrate the modified starting value sequences to the target database.
* If the modified sequence starting values are within a reasonable range, clicking **[ save and override ]** will re-fetch the migration task, and the sequences with modified starting values will be migrated to the target database in an overwrite manner.
* If the modified sequence starting values exceed the maximum value (maxValue) or upper limit (9999999999999999999999999999), clicking **[ save and override ]** will pop up a window displaying all unreasonable modified sequence starting values, allowing users to confirm whether to continue migration:
  * Clicking **[ confirm ]** will continue the migration of the sequences in an overwrite manner, and those with unreasonable starting values will report errors accordingly, while reasonable sequences will continue to migrate successfully.
  * Clicking **[ cancel ]** will return to the sequence starting value modification page for further operations.
* Confirm repair: For objects with failed metadata migration, if the user performs manual repairs on the target side, they can manually mark this in the YMP migration results.

  * After migration completion, the **[ confirm repair ]** button in the operation column can be used to manually repair the status of objects with failed metadata migration.
  * After the status change, the displayed progress and report will count the manually repaired objects as completed migrations and refresh the progress synchronously.

## Migration Completion

After the data migration is completed, if you want to obtain more information, you can check the task logs and migration reports. In the task details, click **[Download]** of the migration report, and after completion, you can view the migration report of the current task.

The migration report includes basic information about the task, the duration of task execution, metadata completion status, performance monitoring status, and data migration status.

When viewing the migration report online, you can click **[Details]** to see the migration information for each object and filter by object type, migration result, error information, metadata transformation, conflict handling, transformation rules, source Schema, and target Schema, along with search capabilities for source object names, target object names, source table names, target table names, and migration duration.

### Migration Retry

Migration tasks can initiate a retry after completion, failure, or termination:

* It includes all objects that are not successfully migrated (failed, pending migration, terminated); users need to manually select the range of objects and cannot change other configurations of the YMP migration task.
* Only objects that were not successfully migrated will be retried; even if the retry range includes successfully migrated objects, they will not be retried. However, for tables, if both metadata and data migrations are selected, and metadata migration is successful but data migration is not performed (for example, if the task is terminated), the condition filtering needs to check the successfully migrated objects for the retry. The retry will only continue data migration and not perform metadata migration again.
* During the retry and after completion, the range of migration objects will remain unchanged, consistent with the range initiated during the first migration; the retry action will only refresh the status of the objects involved in this retry, while the status of other objects will not refresh.
* Follows object conflict configurations and data truncation configurations.
* For retrying tasks, performance monitoring only records the performance data for this retry.

* Only tasks with truncation can retry data. If the strategy is not truncation, the retry button can be clicked, but there will be no data retry for tables that failed data migration.

The following are the retry strategies for different object types:

* Table objects:
  * For metadata migration failures/terminations, follow the metadata conflict strategy.
  * For data migration failures/terminations, do not rebuild the table, only clear the table and then retry the data migration.
* Procedure-like objects:
  * If the strategy is overwrite, follow the overwrite strategy.
  * If the strategy is skip, and YMP created failed objects, follow the overwrite strategy; otherwise, follow the skip strategy.
* Other general objects:
  * Follow the metadata conflict strategy.

### Manual Repair

For objects with failed metadata migration, if the user directly enters the target side to perform repairs, they can manually mark this in the YMP migration results (the **[ confirm repair ]** button appears in the operation column).

Once manual marking is completed, the migration status of the metadata will change, and the migration report will adjust the status of that object to completed migration, refreshing the progress synchronously.

### Return to Previous Step

If you click **[Previous]** on the page, you will return to the migration configuration phase. The current migration data will not be saved, and clicking **[Next: Data Migration]** will allow you to re-initiate the migration.

### Click Next Step

After successfully completing the data migration, click **[ Next: Validation Initialization ]** to enter the validation initialization page for configuration.


