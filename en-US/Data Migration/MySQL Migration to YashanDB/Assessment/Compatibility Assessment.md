> **Note**:
>
> 1. MySQL allows Events to have the same name as other objects, but YashanDB does not support this feature.
>
> 2. For Events with different names, even if the compatibility assessment passes, migrating may cause the corresponding Job's PL in YashanDB to fail during execution. Therefore, it is still necessary to analyze the obtained source DDL to confirm whether rewriting is needed.
>
> 3. Some types of objects can derive other types of objects for evaluation, such as auto-increment columns deriving sequences and column default values "default on update" deriving triggers.



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





### Object Details

Displays the assessment results of all objects, including object type, object name, related tables, etc.

- View DDL: You can view the source DDL, target DDL, and error messages.
  
  * Reset will revert the target DDL to the DDL statement after the first assessment.
  * Copy will copy the DDL to the clipboard.
  * You can modify the target DDL and proceed with operations afterward.
  * Save only will save the modified DDL.
  * Validate and save will execute the modified DDL in the assessment database, and the execution results will be displayed above.

- Update Validation DDL: Select objects to re-fetch the source DDL for validation; after updating validation DDL, a popup will display the count of successful, failed, deleted, added, unexecuted cases, and object names.
  
  * This operation requires a forced full refresh of the report.
  * When updating the validation DDL for tables, related constraint indexes will be updated simultaneously.

- Ignore: Modify the ignore status of the current object; the effect is the same as whether the current filtering result is ignored or not.

- Reset: Will restore a single object's DDL to the style after the first assessment completion.
  
  * The object will be marked as unverified.
  * This operation requires a forced full refresh of the report.
  * This operation will only reset the DDL of objects that have not been checked.

- Check: Users can check the manually fixed DDL and mark it as checked status.
  
  * If the object compatibility result is manually compatible, the object compatibility statistics, PL code statistics, and incompatible type statistics will simultaneously refresh the compatibility ratio. If you need to refresh the report download to include the latest report content, you need to use the **[ regenerate report ]** functionality to refresh it.
  * If the object has been **[ ignore ]**, then the **[ Check ]** operation cannot be performed. **[ ignore ]** means that the result of compatibility for this object is no longer a concern, while **[ checked ]** indicates that this object is of concern, and manual fixes have been confirmed.
  * Checked objects do not support DDL reset or modification operations.
    
    > **Note**：
    > 
    > 1. When the assessment time is short, it is recommended to use the reassess functionality to get source DDL changes; when the assessment time is long and the number of assessed objects is large, use the bulk update validation DDL functionality.
    > 2. Users and privileges do not support bulk update validation DDL functionality.
    > 3. Constraints and derived objects do not support separate bulk update validation of DDL; when updating the validation of constraints, it is necessary to select the associated tables together.
    > 4. Operations like ignore and DDL will change the modification time of objects, and when the object modification time is later than the last assessment time, it will prompt for a full refresh of the report.

- Bulk Validation: You can select multiple objects to validate their compatibility; after validation, a popup will show the count of successful, failed, unexecuted cases, and object names.
  
  * The results of bulk validation will not change compatibility statistics.
  * This operation requires a forced full refresh of the report.
  * When the number of selected objects exceeds 3000, only 3000 objects will be validated.
  * After validation is complete, you need to re-select the objects for the next validation.

- Bulk Update Validation DDL: You can select multiple objects to re-fetch the source DDL for validation, and after updating validation DDL, a popup will display the count of successful, failed, deleted, added, unexecuted cases, and object names.
  
  * The results of bulk update validation DDL will not change compatibility statistics.
  * This operation requires a forced full refresh of the report.
  * When the number of selected objects exceeds 3000, only 3000 objects will be validated.
  * After updating validation DDL, you need to re-select the objects for the next update validation.
  * When updating the DDL of the validation table, related constraint indexes will also be updated.
  * The full refresh of the report will remove non-existent objects after bulk update validation DDL.

- Bulk Modification: Supports modifying text and the sequence starting value.
  
  - Text Replacement:
  
    - Users can choose the objects to modify and perform string or regex replacement on the target DDL of the objects.
    - A maximum of 20 rules is allowed for bulk modification.
    - Selected rules will execute sequentially from top to bottom, matching and replacing in the target DDL.
    - The left side of the rule list shows the string or regex pattern to be replaced, while the right side shows the new string for replacement.
    - If the modified object has not been validated, there will be a red dot indicator in the View DDL section showing that it is unverified.
    - This operation requires a forced full refresh of the report.
    - This operation will only reset the DDL of objects that have not been checked.
  
  - Sequence Starting Value Modification:
  
    * Users can select the sequence objects to modify the starting value of the target DDL sequence.
    * Users can set the increment for the starting value of the sequence, which will be added to the existing starting value.  
    * The increment for the sequence starting value must be greater than or equal to 0, and the modified starting value cannot exceed the upper limit (9999999999999999999999999999). 
    * This operation requires a forced full refresh of the report.
  
- Bulk Reset: Users can choose the objects to modify and reset the target DDL of the objects in bulk.

  * The DDL of the reset objects will revert to the style after the first assessment completion.
  * The objects will be marked as unverified.
  * This operation requires a forced full refresh of the report.
  * This operation will only reset the DDL of objects that have not been checked.

- Bulk Download DDL: This operation will download the DDL of all objects meeting the filter criteria based on current filtering conditions.

  * The download result will be in a zip package; the zip package will categorize files based on the object's schema and type, distinguishing between source, first assessment target, and current target statements.
  * Procedures and packages will each be written into one file, while other object types will be written into one file together.

- Bulk Set Whether to Ignore: Can perform ignore operations based on current filtering conditions on all object DDLs meeting filter results.

  * Ignoring will change the **[ whether ignore ]** status of the object list to ignored, and these objects will not be assessed again during full report refresh.
  * Not ignoring will change the **[ whether ignore ]** status of the object list to not ignored, and these objects will be assessed again during full report refresh.





### Return to Previous Step

Clicking **[ Previous ]** will return the page to the assessment configuration phase, and the current assessment data will not be retained.

### Click Next Step

Clicking **[ Next: Migration Configuration ]** will return the system to the following results based on the compatibility assessment results:

- If the assessment results are fully compatible, and no object information has been modified, the system jumps to the migration configuration page.
- If the assessment results are not fully compatible, the system will prompt in a popup, and clicking **[ confirm ]** will redirect to the assessment configuration page.
- If the assessment results are fully compatible, but some object information has been modified, the system will prompt in a popup. Clicking **[ Proceed to Migration Configuration ]** will redirect to the assessment configuration page. Clicking **[ yes ]** will trigger a full report refresh operation.


