

Before starting the validation task, the verification phase requires entering the **[Validation Initialization]** page to configure various options. After completing all configurations, click **[Next: Consistency Validation]** to initiate the validation task.

### Basic Information

The **[Basic Information]** area on the validation initialization page displays the basic information of the migration task, including task name, database name, etc.

For validation tasks after the migration phase, this area of information is not allowed to be modified.

For independent validation tasks, it is allowed to modify the target database information here, and modifications are prohibited after the validation task starts running.

### Select Validation Scope

This area displays a Schema list for users to select the scope to be validated:

- For validation tasks after the migration phase, the displayed list comes from the migration range selected during the migration phase, and the validation scope is selected by default.
- For independent validation tasks, the displayed list includes all Schemas in the database, and the validation scope is not selected by default. After checking the range, you need to click **[confirm range]** in the upper right corner to generate the validation objects.

The validation scope selected in this area is based on the Schema level, and adjustments to the object-level validation scope can be made in the subsequent validation object list.

### Validation Task Configuration

Validation task configuration includes data validation type configuration and performance configuration:

- Data validation type configuration: This configuration choice will affect the layout and functionality of the consistency check page.
  * **[Full Validation]**: Perform a full data consistency check on the tables of the source database and the target database. The consistency check page displays the full check.
  * **[Primary Key Validation]**: Perform consistency check on the primary keys/unique keys of the tables in the source database and the target database. The consistency check page displays the primary key check.
  * **[Statistical Validation]**: Count and compare the number of rows in the tables of the source database and the target database, and the consistency check page displays the statistical check.



For DB2 source-end validation task configuration, only **[Statistical Validation]** can be selected.

* Performance Configuration: Used to configure the maximum number of table validations that can run concurrently. One thread is used for validating one table, and one database connection is occupied by both the source-end and target-end. The default value is 50, and the range of values is [1,200].

### Validation Table Configuration

This area will list all table-level validation task configurations provided by YMP. For DB2 source-end validation tasks, the following items can be configured:

* **[Single Table Inconsistency Threshold]**: You can choose to have no limit or input a custom threshold. The validation task will be aborted upon reaching the threshold.
* **[Large Table]**: Degree of concurrency for large table splitting: The maximum number of sub-split tables that can run concurrently, with a default value of 10 and a range of [2,15]; number of rows for large table splitting: The row count that can be used as a sub-split item, with a default value of 50000000 and a range of [1,80000000].
* **[Character Type Options]**: Trimming right-side spaces of CHAR data type is enabled by default, and trimming right-side spaces of VARCHAR data type is disabled by default.
* **[Numeric Type Options]**: FLOAT data type validation precision has a default value of 6, with a range of [1,10]; DOUBLE data type validation precision has a default value of 15, with a range of [1,20].
* **[Enable Periodic Validation]**: Periodic validation can be configured with specific times and cron expressions, allowing tasks to carry out periodic validation, supporting both normal validation and flashback validation.



### Select Validation Objects

This area lists all table objects under the Schema within the validation scope for object-level validation configuration:

* For the verification tasks after the migration phase, table objects that have been successfully migrated will be set to the verification state by default, and table objects that have failed to migrate will be set to the non-verification state by default.
* For independent validation tasks, all table objects are set to validation status by default.
* You can filter by Schema name, whether to validate, and support for table column width stretching.
* The upper right corner can configure case sensitivity. When not ignoring case, both the source and target ends are case-sensitive; when ignoring case, the source end is case-sensitive, and the target end will query uniformly in uppercase.
* Filtering conditions can be set for individual tables, and the validation will compare the columns after applying the filtering conditions. Batch settings are also supported.
* Each table can be configured with **[Table-Level Configuration]** that follows the same rules as mentioned above, except for periodic validation.


