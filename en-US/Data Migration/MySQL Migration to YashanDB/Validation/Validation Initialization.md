

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



* Performance Configuration: This is used to configure the maximum number of parallel table verifications. Each table verification uses one thread, and both the source and target ends occupy one database connection each. The default value is 50, with a range of [1,200].

### Table Verification Configuration

This area lists all table-level verification task configurations provided by YMP. For verification tasks on the MySQL source end, the following items can be configured:

* **[ Single Table Inconsistency Threshold ]**: You can check to allow unlimited or input a custom threshold. The verification task will be terminated when the threshold is reached.
* **[ Large Table ]**: Large table splitting concurrency: The maximum number of sub-split tables that can run simultaneously, default value is 10, and the range is [2,15]; Number of rows for large table splitting: The number of rows at which a sub-split can be made, default value is 50000000, and the range is [1,80000000].
* **[ Zero Value Validation Settings ]**: Whether to convert the 0 values of Time, Date, DATETIME, and TIMESTAMP types to NULL values for validation, default is enabled.
* **[ NULL Validation ]**: Whether to convert empty strings to NULL values for validation, default is enabled.
* **[ Character Type Options ]**: Removing trailing spaces from CHAR data type is enabled by default, while removing trailing spaces from VARCHAR data type is disabled by default.
* **[ Numeric Type Options ]**: The default verification precision for FLOAT data type is 6, with a range of [1,10]; The default verification precision for DOUBLE data type is 15, with a range of [1,20].
* **[ Enable Periodic Validation ]**: Periodic verification can be configured by setting specific times and cron expressions, allowing the task to perform periodic verifications. It supports both regular verification and flashback verification as two types of periodic verification methods.
* **[Number and Boolean compatible validation]**: Supports cross-type consistency verification between integer values and boolean values for columns with the same name that are non-validation sort keys on the source and target sides.(MySQL's BOOLEAN (also abbreviated as BOOL) is only an alias for TINYINT(1), so validation is performed according to the TINYINT type.)



### Select Validation Objects

This area lists all table objects under the Schema within the validation scope for object-level validation configuration:

* For the verification tasks after the migration phase, table objects that have been successfully migrated will be set to the verification state by default, and table objects that have failed to migrate will be set to the non-verification state by default.
* For independent validation tasks, all table objects are set to validation status by default.
* You can filter by Schema name, whether to validate, and support for table column width stretching.
* The upper right corner can configure case sensitivity. When not ignoring case, both the source and target ends are case-sensitive; when ignoring case, the source end is case-sensitive, and the target end will query uniformly in uppercase.
* Filtering conditions can be set for individual tables, and the validation will compare the columns after applying the filtering conditions. Batch settings are also supported.
* Each table can be configured with **[Table-Level Configuration]** that follows the same rules as mentioned above, except for periodic validation.


