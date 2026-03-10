> **Note**:
>
> - When executing privilege assessment, statements with high-level packages on the source side will be commented out for filtering.
>
> - Some types of objects can derive other types of objects to participate in the assessment, such as an auto-increment column deriving a sequence.
>
> - For compatibility assessment on the Oracle source side, after the assessment is completed, you can execute **[ Database refresh object status ]**. This operation will refresh the status of all objects to remain consistent with the source database. The status values include VALID, INVALID, and --, where -- indicates that the object has no status.



The compatibility assessment task serves as the first phase of the full migration task. It can be initiated without assessment configuration, but it is essential to ensure that [Preparation Before Migration](../Preparation Before Migration/00Preparation Before Migration) has been completed.

## Start Assessment

Once the assessment task is initiated, the page will display the progress information of the assessment and support the following intervention operations:

- **[Suspend Assessment]**: Tasks that are in the assessment can click the **[ suspension assessment ]** button below to stop the task or click **[ pause ]** in the operation bar of the task list.
- **[Resume Assessment]**: After the task is paused, the **[ recovery assessment ]** button can be clicked to continue the current task. The recovery after pausing may lead to inaccurate PL code statistics, which can be resolved later by refreshing or reevaluating.
- **[Re-Assess]**: After terminating the assessment task, clicking **[ reassess ]** will redirect the page to the assessment configuration phase.

## Assessment Completion

Assessment completion includes two scenarios: assessment failure and assessment success:

- Assessment Failure: When the assessment task fails to end normally, the task status on the page will change to the assessment failure status.
- Assessment Success: The normal completion of the assessment task indicates assessment success, and the system will automatically redirect to the assessment completion page.

The following information is presented on the assessment completion page.

### Basic Information

Displays the task's basic information, task phase, assessment time, and the database information used.

- **[edit]**: You can edit the basic information of the task.

- **[download]**: You can choose to download the complete assessment report or the incompatibility report.

- **[full refresh]**: After manually modifying the DDL, you can click to perform a full refresh.
  
  * If there are modifications or edits to the DDL of the object details, it will update the last modified time of the task's basic information, prompting a full refresh.
  * A full refresh will re-evaluate all non-ignored objects, at which time the task status will be "assessment refreshing."
  * After the refresh is complete, the last assessment time will change.
  * Only one full refresh operation is allowed at the same time.
  * A full refresh will update the overall situation of the current task report and the overall object distribution and schema summary statistics.
  * The refresh process can be terminated, and the task status will change to refresh failure.

- **[ regenerate report ]**: This functionality is equivalent to the report regeneration functionality in report management.
  
  * Displayed during assessment, it refreshes the object compatibility statistics, PL code statistics, and incompatible type statistics for objects manually made compatible after **[Check]**, requiring a manual trigger for report regeneration.

### Overall Situation

Displays the statistics of object compatibility for the task, PL code compatibility statistics, incompatible type statistics, total data volume for migration, and estimated migration time.

If the **[Compatibility]** shown on the page does not reach 100%, click **[object details]** to view the incompatible objects, choose to modify or ignore in bulk as needed, and then click **[full refresh]**.

**[Compatibility]**When the value does not reach 100%, you can still proceed with subsequent tasks. However, during the data migration phase, errors may occur due to incompatibility of a certain object. It is recommended that you ensure 100% compatibility during evaluation.

- Object compatibility statistics: Count of objects by different compatibility types and compatibility ratios.
  
  - Native Compatibility: Source DDL can be successfully executed in the target database.
  - Automatic Compatibility: Source DDL can be successfully executed in the target database after being converted by YMP's built-in SQL tools.
  - Manual Compatibility: Source DDL is incompatible and is manually modified to compatible DDL on the YMP page.
  - Derived Compatibility: Objects that were not originally present on the source side due to database differences and other reasons, where YashanDB performs syntax conversion to create new objects.
  - Incompatible: DDL that cannot be compatible using automatic conversion or manual modification.
  - Derived Incompatibility: Manually changing derived objects to incompatible status means derived incompatibility.

- PL code statistics: PL code compatibility statistics.

- Total data volume for migration: Total data volume to be migrated from the source.

- Estimated migration time: Time required to complete the data migration from the source.

- Overall object distribution: Displays the types, quantities, and compatibility status of objects to be migrated from the source.

- Schema Summary: Categorizes and counts the assessment results by schema and object type, displaying the number and ratio of compatible objects of different types under each schema. It also counts the data volume and migration time for each schema.



- Batch incremental check: Supports batch selection of objects and verification of their incremental compatibility. If the objects to be migrated contain items that have not been verified and have not undergone incremental evaluation, a prompt pop-up will appear after clicking **[Batch Increment Validation]**.

  * The results of batch incremental checks will not change the compatibility statistics.
  * After the inspection is completed, it is necessary to reselect objects in batches for the next inspection.



### Return to Previous Step

Clicking **[ Previous ]** will return the page to the assessment configuration phase, and the current assessment data will not be retained.

### Click Next Step

Clicking **[ Next: Migration Configuration ]** will return the system to the following results based on the compatibility assessment results:

- If the assessment results are fully compatible, and no object information has been modified, the system jumps to the migration configuration page.
- If the assessment results are not fully compatible, the system will prompt in a popup, and clicking **[ confirm ]** will redirect to the assessment configuration page.
- If the assessment results are fully compatible, but some object information has been modified, the system will prompt in a popup. Clicking **[ Proceed to Migration Configuration ]** will redirect to the assessment configuration page. Clicking **[ yes ]** will trigger a full report refresh operation.


