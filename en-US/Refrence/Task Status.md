Evaluation, migration, and validation in YMP are all based on tasks. This section introduces the various states of a task.

### Create Task - Success

After saving the task configuration in the create task interface, the task is generated with the status "Success." 

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|--------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Success         | Save the task, status remains unchanged  |
| (Task starts with migration) Migration Configuration   | Create Task - Retrieving Objects     |                                  |
| Start Assessment Before Migration          | Assessment Before Migration - Evaluating         |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Evaluation Configuration - In Configuration
After entering the evaluation configuration interface, the task status changes to "configuring".

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|--------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Success         | Save the task, status remains unchanged  |
| (Task starts with migration) Migration Configuration   | Create Task - Retrieving Objects     |                                  |
| Start Assessment Before Migration          | Assessment Before Migration - Evaluating         |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Assessment Before Migration - Evaluating

After clicking on Start Assessment Before Migration, the task status changes to "Evaluating."  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Terminate Task              | Assessment Before Migration - Terminated      |                                  |

### Assessment Before Migration - Pause

In evaluating tasks, after the terminate assessment operation, the task has entered a paused state. 

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| Start Assessment Before Migration          | Assessment Before Migration - Evaluating         |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Assessment Before Migration - Evaluation failed

If an exception occurs during the evaluation process, the task status changes to failed, and a failure log will be recorded.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| Start Assessment Before Migration          | Assessment Before Migration - Evaluating         |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Assessment Before Migration - Refreshing

After refreshing the report, the task enters the "Evaluating Refreshing" state, at which point the task will reassess based on changes.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Terminate Task              | Assessment Before Migration - Refresh Terminated      |                                  |

### Assessment Before Migration - Refresh Pause

In tasks with a refreshing evaluation, after the terminate assessment operation, the task has entered a refresh pause state.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Assessment Before Migration - Refresh Failed

If an exception occurs during the refresh evaluation process, the task status changes to failed, and a failure log will be recorded.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Assessment Before Migration - Completed

After the evaluation is completed, the evaluation results are generated, and the status changes to completed.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Snapshot Migration - Initializing     |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Migration Configuration - Configuring

In the migration configuration interface, saving the configuration information generates the migration task, and the status is "Configuring Migration."  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration | Create Task - Retrieving Objects     |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Snapshot Migration - Initializing     |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Migration Configuration - Retrieving Objects

Creating a task that starts with migration will immediately change the task status to "Retrieving Objects in Migration Configuration."  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| No operations allowed            | --                            |                                  |

### Migration Configuration - Object Retrieval Failed

If an exception occurs during the object retrieval process for a task that starts with migration, the task status changes to "Object Retrieval Failed."  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Migration Configuration - Retrieving Objects |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Full migration - Initializing

After starting full migration, the task first enters the initializing state, performing preliminary tasks like creating tablespaces before migration and querying the data volume of data migration tables. This state is part of the data migration process.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Pause Task                     | Full Migration - Paused |                                  |
| Terminate Task                 | Full Migration - Terminated |                                  |

### Full Migration - Object Migration

When full migration begins migrating objects other than primary key constraints, unique constraints, foreign key constraints, indexes, and triggers, the status changes to "Object migration" which is part of the data migration process.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Pause Task                     | Full Migration - Paused |                                  |
| Terminate Task                 | Full Migration - Terminated |                                  |

### Full Migration - Data Migration

When full migration starts migrating table data, the status changes to "Data Migration," which is part of the data migration process.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Pause Task                     | Full Migration - Paused     |                                  |
| Terminate Task                 | Full Migration - Terminated |                                  |

### Full Migration - Supplement object integrity

When full migration starts migrating primary key constraints, unique constraints, foreign key constraints, indexes, and triggers, the status changes to "Metadata Migration Stage 2," which is part of the data migration process.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Pause Task                     | Full Migration - Paused     |                                  |
| Terminate Task                 | Full Migration - Terminated |                                  |

### Full Migration - Terminated

In full migration, tasks enter a temporary terminated state after the terminate operation is performed.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| No operations allowed            | --                            |                                  |

### Full Migration - Terminating

In the terminating state, when the active sessions of the migration are interrupted, and the source and target database connections are closed, the task enters the terminated state.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Create Task - Retrieving Objects |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Full Migration - Initializing |                                  |
| Retry Migration                | Full Migration - Initializing     |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Full Migration - Paused

In full migration, when the pause operation is performed, the task briefly enters a paused state.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| No operations allowed            | --                            |                                  |

### Data Migration - Paused State

In a paused state, the active sessions of the migration are interrupted, and the source and target database connections are closed.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|------|------------|------|
| Terminate Task               | Full Migration - Terminated  |                                  |
| Resume Task                  | Full Migration - Initializing |                                  |
| Delete Task                  | Task Deleted, No Status           |                                  |

### Data Migration - Failed

If an exception occurs during the full migration completed. migration process, the task status changes to failed, and a failure log will be recorded.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Create Task - Retrieving Objects |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Full Migration - Initializing      |                                  |
| Retry Migration                | Full Migration - Initializing     |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Data Migration - Completed

Full migration completed.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|------------|------------| ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Create Task - Retrieving Objects |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Full Migration - Initializing     |                                  |
| Retry Migration                | Full Migration - Initializing    |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |
| Validation Initialization      | Validation - Pending Validation     |                                  |

### Incremental migration-initializing

After starting the incremental migration, the task will first enter the initialization state, where it performs initialization work such as creating tablespaces before migration and querying the data volume of data migration tables. This state is part of the data migration in-progress state.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
| -------------------------------- | ----------------------------------------- | ------------------------ |
| Pause Task                       | Incremental Migration - Paused            |                          |
| Terminate Task                   | Incremental Migration - Terminated        |                          |

### Incremental migration-executing

When incremental migration starts migrating table data, the status enters "in execution", which belongs to the "data migrating" status.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
| -------------------------------- | ----------------------------------------- | ------------------------ |
| Pause Task                       | Incremental Migration - Paused            |                          |
| Terminate Task                   | Incremental Migration - Terminated        |                          |

### Incremental migration-paused

For tasks in incremental migration, after performing a pause operation, the active sessions that interrupt the migration will be terminated, and the database connections at the source and target ends will be closed, after which the task will enter a paused state.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
| -------------------------------- | ----------------------------------------- | ------------------------ |
| Terminate Task                   | Incremental Migration - Terminated        |                          |
| Resume Task                      | Incremental Migration - Initializing      |                          |
| Delete Task                      | Task Deleted, No Status                   |                          |

### Incremental migration-terminated

For tasks in incremental migration, after a termination operation is performed, the active sessions that were interrupting the migration will be interrupted, and the database connections on the source and target ends will be closed, after which the task will enter the terminated state.

In the current state, the following operations can be performed:

|Optional Operations                     |Next Task Status |Description |
| ---------------------------------------------------- | ----------------------------------------- | ------------------------ |
| Save Task                                            | Create Task - Configuring                 |                          |
| (Task starts with migration) Migration Configuration | Create Task - Retrieving Objects          |                          |
| Start Assessment Before Migration                    | Assessment Before Migration - Evaluating  |                          |
| Refresh Report                                       | Assessment Before Migration - Refreshing  |                          |
| Save Migration Configuration                         | Migration Configuration - Configuring     |                          |
| Snapshot Migration                                   | Incremental Migration - Initializing      |                          |
| Retry Migration                                      | Incremental Migration - Initializing      |                          |
| Delete Task                                          | Task Deleted, No Status                   |                          |

### Incremental migration-failed

During the incremental migration process, if a task-level exception occurs and the task status becomes failed, a failure log will be recorded.

In the current state, the following operations can be performed:

|Optional Operations                     |Next Task Status |Description |
| ---------------------------------------------------- | ----------------------------------------- | ------------------------ |
| Save Task                                            | Create Task - Configuring                 |                          |
| (Task starts with migration) Migration Configuration | Create Task - Retrieving Objects          |                          |
| Start Assessment Before Migration                    | Assessment Before Migration - Evaluating  |                          |
| Refresh Report                                       | Assessment Before Migration - Refreshing  |                          |
| Save Migration Configuration                         | Migration Configuration - Configuring     |                          |
| Snapshot Migration                                   | Incremental Migration - Initializing      |                          |
| Retry Migration                                      | Incremental Migration - Initializing      |                          |
| Delete Task                                          | Task Deleted, No Status                   |                          |

### Verification - Pending Validation

After the data migration is completed, the next step enters a pending validation state.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|---------|------------| ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Create Task - Retrieving Objects |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Snapshot Migration - Initializing     |                                  |
| Retry Migration                | Snapshot Migration - Initializing     |                                  |
| Validation Initialization      | Validation - Pending Validation     | Existing initialization operation for validation tasks does not change the task status |
| Start Consistency Check       | Validation - Checking                |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Verification - Checking

After starting the consistency check, the task enters the "Consistency Check - Checking" state.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| Validation Initialization      | Validation - Checking                | Existing initialization operation for validation tasks does not change the task status |
| Terminate Task                | Consistency Check - Termination in Progress  |                                  |

### Verification - Completed

When the consistency check is completed, the status changes to "Consistency Check - Completed."

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|---------|------------| ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Create Task - Retrieving Objects |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Snapshot Migration - Initializing     |                                  |
| Retry Migration                | Snapshot Migration - Initializing     |                                  |
| Validation Initialization      | Validation - Completed  | Existing initialization operation for validation tasks does not change the task status |
| Start Consistency Check       | Validation - Checking                |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Validation - Exception

When the consistency check is completed, but errors occur during validation.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|---------|------------| ------------------------ |
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Create Task - Retrieving Objects |                                  |
| Start Assessment Before Migration      | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration     | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Snapshot Migration - Initializing     |                                  |
| Retry Migration                | Snapshot Migration - Initializing     |                                  |
| Validation Initialization      | Validation - Failed  | Existing initialization operation for validation tasks does not change the task status |
| Start Consistency Check       | Validation - Checking                |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Verification - Termination

After the termination of the consistency check task, it enters the paused state.  

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|------------------|------------|-----|
| Save Task                    | Create Task - Configuring         |                                  |
| (Task starts with migration) Migration Configuration  | Create Task - Retrieving Objects |                                  |
| Start Assessment Before Migration            | Assessment Before Migration - Evaluating         |                                  |
| Refresh Report                | Assessment Before Migration - Refreshing         |                                  |
| Save Migration Configuration           | Migration Configuration - Configuring         |                                  |
| Snapshot Migration          | Snapshot Migration - Initializing     |                                  |
| Retry Migration             | Snapshot Migration - Initializing     |                                  |
| Validation Initialization      | Consistency Check - Terminated  | Existing initialization operation for validation tasks does not change the task status |
| Start Consistency Check          | Validation - Checking                |                                  |
| Delete Task                   | Task Deleted, No Status           |                                  |

### Task-deleting
After clicking **[Delete]**, the task will enter the deleting state.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| No operations allowed            | --                            |                                  |

### Task-delete failed
An exception occurred during the deletion process, and the task status changed to deletion failed. You can click **[Delete]** again to re-enter the deleting status.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|---------|------------| ------------------------ |
| Delete Task                    | The task has been deleted, no status.         |

### Task-operating
During the evaluation phase, after the batch operation is completed, a "Task - In Operation" status will appear.

In the current state, the following operations can be performed:

|Optional Operations |Next Task Status |Description |
|----------------------------| -------------------------- | ------------------------ |
| No operations allowed            | --                                        |                          |