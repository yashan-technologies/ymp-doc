In the YMP fault tolerance mechanism, a unified set of error message codes is provided for errors that require timely user awareness. The format is as follows:

Error Number: Composed of 'YMP-' + five-digit numbers, with the five digits partitioned by different modules.

Error Message: Description of the error information. In different error scenarios, the system conveys specific information about the error through variables.

## YMP-00000

**Message**: Success.

**Action**: No exceptions, no action needed.

## YMP-00001

**Message**: Parameter %s is out of the supported range: %s.

**Action**: The interface or configuration parameter is not within the system's supported range, adjust and retry.

## YMP-00002

**Message**: File upload failed, %s.

**Action**: The uploaded file does not meet the requirements, please select a suitable file to upload according to the prompts.

## YMP-00003

**Message**: Exception connecting to built-in/external library: %s.

**Action**: Please check the connection status of the built-in/external library.

## YMP-00004

**Message**: Version %s is not supported: %s.

**Action**: The operation is not supported in this version.

## YMP-00005

**Message**: File %s no longer exists.

**Action**: The system resource being operated on (logs, reports, files, etc.) has been cleared.

## YMP-00006

**Message**: Feature not implemented: %s.

**Action**: The system feature is not implemented, or certain features of the Data Source type are not supported.

## YMP-00007

**Message**：process file error: %s.

**Action**: File processing error, please adjust the file content as required.

## YMP-00008

**Message**： %s port has been used.

**Action**: Please modify or release the port.

## YMP-00009

**Message**：exist conflict: %s.

**Action**: Please address the conflict according to the error details.

## YMP-00010

**Message**：insufficient resources: %s.

**Action**: Please adjust the relevant resource allocation.

## YMP-00011

**Message**: This operation is not supported: %s.

**Action**: This operation is not supported.

## YMP-01000

**Message**: User does not exist.

**Action**: The user operating does not exist, please check the username.

## YMP-01001

**Message**: Username or password is incorrect.

**Action**: Username or password is incorrect, please verify and retry.

## YMP-01002

**Message**: Password retry exceeded 5 times, please reset the password.

**Action**: Password retry exceeded 5 times, please reset the password and restart YMP to use.

## YMP-01003

**Message**: %s password cannot be empty.

**Action**: Password cannot be empty.

## YMP-01004

**Message**: New password cannot be the same as old password.

**Action**: New password cannot be the same as old password.

## YMP-02000

**Message**: %s Data Source cannot connect: %s.

**Action**: The source or target Data Source being used has connection issues, please check the database can be connected and used normally before retrying.

## YMP-02001

**Message**: %s connection user %s does not have sufficient %s Privilege.

**Action**: The connection user's Privilege on the source or target database is insufficient, please grant privileges and retry.

## YMP-02003

**Message**: Data Source is being used by %s task [%s], only supports %s.

**Action**: Data Source is in use, only certain operations are supported, such as modifying other items, please delete the task before proceeding.

## YMP-02004

**Message**: A Data Source with the same name already exists.

**Action**: A Data Source with the same name already exists, please modify the Data Source name.

## YMP-02005

**Message**: The Data Source [%s] pointing to the database (%s) has been used as %s by task [%s].

**Action**: A Data Source for the same database (which may be multiple) cannot be used simultaneously as [Source Database] and as [Assessment Database or target database for migration]. Please change the database or delete other unused tasks that are using this source.

## YMP-02006

**Message**: Data Source version not supported: %s.

**Action**: The Data Source version does not match the configuration, please use a supported version.

## YMP-02007

**Message**: The required number of connections for %s task exceeds the rated connection limit of %s Data Source: %s.

**Action**: The required number of connections for the task exceeds the maximum connections allowed per Data Source at this stage. Please wait for the running tasks to complete, reduce task parallelism, or increase the maximum connection threshold.

## YMP-02008

**Message**: The available connections for %s Data Source cannot meet the required connections for %s task.

**Action**: The Data Source cannot currently provide the required connections for the task, please clean up unused connections or increase the Data Source's maximum connection limit.

## YMP-03000

**Message**: The corresponding %s task does not exist.

**Action**: The operation's corresponding task has been deleted or modified, please refresh the homepage to view the latest data.

## YMP-03001

**Message**: The task is in %s status, cannot perform %s operation.

**Action**: The task is in a non-interruptible running phase, operations cannot be performed temporarily, please retry later.

## YMP-03002

**Message**: Schema: [%s] is occupied by [%s] task %s on the current %s (%s).

**Action**: The same schema can be used only once in an Assessment Database and target migration database, please switch databases and retry.

## YMP-03003

**Message**: The task has been terminated.

**Action**: The task has been terminated, please refresh the homepage to view the latest data.

## YMP-03004

**Message**: The task status has changed from %s to %s.

**Action**: Another operation caused the task status to change, please refresh and retry.

## YMP-03005

**Message**: Task name does not meet the requirements.

**Action**: Task name does not meet the requirements, a maximum of 128 characters is allowed, spaces are not supported, and the task name cannot be empty.

## YMP-03006

**Message**: A task with the same name already exists.

**Action**: A task with the same name already exists, please modify the task name.

## YMP-03007

**Message**: The schema list selected for the task is empty.

**Action**: The schema list selected for the task cannot be empty.

## YMP-03008

**Message**: A case-insensitive schema with the same name exists: [%s].

**Action**: Under case-insensitive circumstances, a schema with the same name exists, posing a risk of data overwrite, please retain only one.

## YMP-03009

**Message**: The %s task depends on a %s Data Source that has not been added.

**Action**: Before starting the task, ensure that the source/target Data Source exists and has been added, click edit to select and switch Data Source.

## YMP-03010

**Message**: The %s task does not have objects to operate on.

**Action**: Before executing the task, ensure there are operable objects within the task scope.

## YMP-03011

**Message**: The structure of table [%s] does not meet validation specifications: %s.

**Action**: The table structure does not meet validation specifications, please modify the table structure according to error prompts.

## YMP-03012

**Message**: Task status is: %s, can only be called in the completed stage.

**Action**: Before executing operations, ensure the task is in the completed stage.

## YMP-04000

**Message**: A schema with the same name exists in the Assessment Database: [%s]. Please confirm whether to overwrite.

**Action**: The assessment will delete the schema with the same name and recreate it, confirmation is required to ensure no data overwrite risk.

## YMP-04001

**Message**: The uploaded assessment file was not found.

**Action**: Offline assessment requires the assessment file to be uploaded first.

## YMP-04002

**Message**: SQL statement is invalid, parsing exception: %s.

**Action**: The SQL statement is invalid, please check the SQL statement itself for issues.

## YMP-04003

**Message**: Database differences, %s.

**Action**: Database feature differences cannot achieve compatibility.

## YMP-04004

**Message**: Failed to delete data from the Assessment Database: %s.

**Action**: Before deleting the task, please check the Assessment Database or confirm whether to skip deleting assessment database data.

## YMP-04005

**Message**: No objects to synchronize, please recheck synchronization objects.

**Action**: Please recheck synchronization objects.

## YMP-04006

**Message**: The exp version is higher than the Source Database version, task creation failed.

**Action**: Please replace with an exp version that is the same as or lower than the source version.

## YMP-05000

**Message**: Tasks that do not undergo assessment cannot perform metadata migration.

**Action**: Tasks that do not undergo assessment cannot perform metadata migration.

## YMP-05001

**Message**: Full refresh report status is required before migration.

**Action**: For tasks that are migrated after assessment, if there are modified objects, a full refresh report status is required before migration.

## YMP-05002

**Message**: Assessment results are below 100%, cannot save migration tasks.

**Action**: For tasks that are migrated after assessment, if the assessment results are below 100%, migration tasks cannot be saved.

## YMP-05003

**Message**: This migration failure scenario does not support manual repair operations.

**Action**: Only metadata migration failures support manual repair status.

## YMP-05004

**Message**: Tasks with exceptions in the migration initialization phase do not support migration retry.

**Action**: For tasks that failed due to exceptions during the migration initialization phase, migration retry is not supported. You can return to the previous step and re-migrate the task.

## YMP-05005

**Message**: The modified Sequence starting value exceeds the upper limit or is greater than maxValue.

**Action**: Use a reasonable value to set the Sequence starting value.

## YMP-05006

**Message**: Data migration SQL validation error: %s.

**Action**: Custom query SQL encountered an error.

## YMP-06001

**Message**: Custom SQL error: %s.

**Action**: An error occurred with custom query SQL.

## YMP-06002

**Message**: Inconsistent data has reached the threshold.

**Action**: Errors have reached the threshold, please correct the errors.

## YMP-06003

**Message**: Illegal value exists on %s side: %s.

**Action**: Modify the illegal value data.

## YMP-99999

**Message**: Internal error, %s.

**Action**: Internal error, please contact our technical support for resolution.