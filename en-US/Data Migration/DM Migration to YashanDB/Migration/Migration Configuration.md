

The migration configuration phase is the preparation stage before data migration, which includes the following three parts:

2. **[ Migration Objects ]**: This section is used to select the objects to be migrated.
4. **[Initialization Configuration]**: This section is used to select the strategy for performing initialization.
6. **[ pre-check ]**: This section is used to select the pre-check to be performed on specified projects before migration.

After completing the above three operations, click **[ Next: Data Migration ]**. Before the task starts, a check will be performed on the selected projects. Once the check passes, the data migration will begin.

## Confirm Migration Objects

### Basic Information

The basic information of the migration task will display the task name, task stage, source database information, target database information, and the time interval since the assessment started.

If the migration task is over 1 hour since the assessment began or has crossed days, **[ Since the last assessment ]** will turn red. Clicking **[ Re-Assess ]** will redirect you to the assessment configuration page for reassessment.

### Migration Scope

This area is used to confirm the steps that need to be migrated. The migration steps include object migration, full data migration, supplementing object integrity. The three stages are checked by default:

* **[ Object Migration ]**: Migrate all definition-level objects such as tables, views, stored procedures, custom functions, and advanced packages.
* **[ Full data migration ]**: Before the task starts, migrate all the existing data from the source end up to the current moment to the target end in one go completely, ensuring that the initial data is consistent.
* **[ Supplement object integrity ]**: Add integrity objects that depend on data or are interdependent, such as primary key constraints, unique constraints, foreign key constraints, indexes, and triggers.

For migration tasks starting from the assessment stage, you can freely select the necessary steps for combined migration here; for tasks that start migration directly without selecting the assessment stage, this area cannot be selected, and data migration will be forced to be checked.





### Select Migration Objects

This area is designated for tasks that start migration directly without the assessment stage. You need to select at least one schema in the listed Schema box, then click **[ confirm range ]**.

The listed Schemas come from the source database.

### Confirm Migration Objects

This area is used to select and confirm the objects to be migrated:

* It supports searching for objects by name and allows the stretching of table column widths.
* It supports filtering objects by object type, assessment results, object status, and whether to migrate.
* It supports batch setting of whether to migrate the selected objects.
* For objects obtained during the assessment stage, it supports viewing the DDL of the objects.



* Supports renaming the Schema (i.e., configuring a target Schema with a name different from the source Schema). The entered name must not exceed 64 characters and can only include English letters, numbers, and the special character _.



For migration tasks starting from the assessment stage, the object list in this area comes from all objects obtained during the assessment stage; for tasks that start migration directly without selecting the assessment stage, the list of objects in this area comes from all objects under the Schema/Database selected in the previous step **[ Select objects ]**.

## Initialization Configuration

This operation is to configure some strategies that need to be executed during the migration process, with the following general principles regarding whether these configurations will be executed:

- When the migration step does not check data migration, the data migration strategy will not take effect and does not need configuration.
- When the migration step does not check data migration, compatibility configuration, performance configuration, and fault-tolerance configuration will not take effect and do not need configuration.

- When the migration step does not check data migration, or if the data migration strategy selects truncate, the compatibility configuration will not take effect and does not need configuration.

- When the object conflict strategy is set to overwrite, the "no truncate" option in the data migration strategy is disabled, and only truncate can be selected.
- For tasks that start migration directly without selecting the assessment stage, the object conflict strategy and privilege conflict strategy will not take effect and do not need configuration.

### Object Conflict Strategy

This area is used to select the execution strategy when object conflicts occur during the migration process, with the default set to **[ skip ]**. The specific strategies are shown in the table below:

|Object Type |Conflict Determination Rules |Strategy to Skip |Strategy to Overwrite |
| ------------------------- | ------------------------------------------------------------ | ---------- | ---------- |
| Constraint - Primary Key      | Existence of a primary key constraint in the same table indicates a conflict  | Keep unchanged    | Delete and recreate   |
| Constraint - Unique Key       | Existence of a unique key in the same column and order indicates a conflict | Keep unchanged    | Delete and recreate   |
| Constraint - Check Constraint  | Same name for check constraints indicates a conflict               | Keep unchanged    | Delete and recreate   |
| Constraint - Foreign Key      | Existence of a foreign key in the same column and order indicates a conflict | Keep unchanged    | Delete and recreate   |
| Constraint - Not Null        | Existence of a not null constraint on the same field indicates a conflict | Keep unchanged    | Delete and recreate   |
| Index - Constraint Built Index  | The index created follows the rules of the constraint, <br> if the constraint creation is successful, it succeeds automatically. <br> Conflict determination and handling are the same as for primary and unique constraints | Follow constraint  | Follow constraint     |
| Index - Other Index          | Existence of an index in the same column and order indicates a conflict | Keep unchanged    | Delete and recreate   |
| Other Objects                 | Determined by object name, case-sensitive                          | Keep unchanged    | Delete and recreate   |

> **Note**:
>
> - If there are differently named check constraints already existing on the target side, regardless of whether the strategy is set to skip or overwrite, the migration task will not delete that check constraint, and users must determine whether to delete it themselves. Data migration must satisfy check constraints on both the source and target sides, otherwise, it will fail.
> - If two foreign keys exist on different columns on the source and target sides, selecting the skip strategy will keep both foreign keys on the target side. During the data migration process, executing foreign key enable may lead to enable foreign key constraints failure: not parent found.

### Data Migration Strategy

This area is used to select the execution strategy when there is already data in the target table. The default is **[ truncate ]**, which will delete existing data; the **[ no truncate ]** strategy will insert directly, which may lead to constraint violations or data duplication risks.

### Privilege Conflict Strategy

This area is used to select the execution strategy when user privilege conflicts occur during the migration process, with the default set to **[ skip ]**. The specific strategies are shown in the table below:

|Conflict Determination Rules |Strategy to Skip |Strategy to Overwrite |Strategy to Merge |
| ------------------------------------ | ---------- | ------------------ | ---------- |
| Conflict occurs when there is an existing user with the same name on the target side | Keep unchanged       | Revoke all privileges and reassign | Assign directly    |

> **Note**:
>
>  Exceptional scenario after migration task is terminated and executed again:
>
> The first migration task has no conflicts, but is terminated after a user is created on the target side; when the migration task is executed again and a privilege conflict occurs, and the conflict strategy is set to skip, according to the strategy, there will be no processing of privileges on the target side. Such a scenario may lead to the target side user having no privileges after migration.

### Tablespace Migration Strategy

This area only appears when the target database and the assessment database are the same database. It is used to select tablespace migration strategies when within the same database, with the default set to **[ skip ]**.

### Advanced Configuration

Advanced configuration items include some non-mandatory configuration options required for migration tasks. Leaving them empty retains the default optimal configuration. The following are several key configuration items:

- **[ Performance ]**: This project allows the selection of the parallelism configuration for migrating multiple tables, with a default of 4 (minimum of 1, maximum of 32). Note that if the parallelism configuration is too high, it may lead to OOM due to insufficient memory. The memory usage during the data migration phase is referenced as follows:

  - For tables with a large number of LOB columns (over 8K), YMP defines that each table should not exceed 1.6GB of memory usage. In this case, if the migration parallelism configuration value is 4, the YMP's ymm_memory parameter value should also be modified (should be configured to 7G or higher). The parameter modifications need to restart YMP to take effect.
  - For tables without a large number of LOB columns (over 8K), YMP defines that each table should not exceed 1GB of memory usage. In this case, if the migration parallelism configuration value is 4, the YMP's ymm_memory parameter value should also be modified (should be configured to 4G or higher). The parameter modifications need to restart YMP to take effect.

- **[ Fault Tolerance Configuration ]**: This project allows setting whether to enable fault tolerance and the error threshold for a single table. When the fault tolerance switch is off, the single table fault tolerance count set is invalid (meaning the tolerance count is 0). When the fault tolerance switch is on, the default tolerance count is 200 (minimum of 0, maximum of 4,294,967,295). Choosing unlimited means setting it to 4,294,967,295. The fault tolerance rules at this time are as follows:

  - If the error count for a single table exceeds this threshold, migration is aborted, and the migration status is marked as failed.

  - If the error count for a single table does not exceed this threshold, migration is not aborted, but the migration status is marked as failed.

  - For tables with data errors, partial error information will be displayed in the migration process and migration report; after the migration, complete error information and complete error data can be downloaded.

  - In the data import stage, due to the use of concurrent imports and batch submissions, the actual fault tolerance data quantity for a single table will generally exceed the fault tolerance threshold but will not exceed the fault tolerance threshold + the number of rows in each CSV file during export as per the configuration.

    > **Note**：
    > This configuration is limited by Database version and Migration Tool version; when the database or tool version is lower than 22.2.7.2, migration fault tolerance is not supported.





- **[ Compatibility Configuration ]**: This project is used to configure compatibility handling for NULL data, which is disabled by default.

  For source data deemed illegal by the target side, YMP will first convert it to NULL before performing the insertion. When compatibility configuration is selected, YMP will also attempt to remove non-null constraints before insertion to avoid data migration failures due to constraint violations.

  > **Note**:
  >
  > When the version of YashanDB on the target side is greater than or equal to 23.3 and the database parameter EMPTY_STRING_AS_NULL is set to FALSE, YMP will insert empty strings from DB2 of CHAR and VARCHAR types directly as empty strings into YashanDB; other types of empty strings will be converted to NULL before insertion.

- **[ Enable Statistics Collection ]**: This project is used to configure whether to perform statistics collection on the target side during the migration process.

  Enabling statistics collection may increase migration duration. If statistics collection is not enabled and there is a large amount of metadata reconstruction, it may result in invalid statistics on the target side, requiring manual re-collection after migration to improve SQL execution efficiency.

- **[ Data Migration Export Tool ]**: This project is used to configure the export tool during migration. The following limitations exist when selecting DTS mode:

  - Due to the limitation of YASLDR version 22.2, data migration in DTS mode does not support passwords containing the '@' character.
  - When migrating TIME ZONE types, DTS selection is not supported, which may lead to incorrect migration results.
  - When migrating BFILE types, DTS selection is not supported, which may lead to migration failure.





### Tablespace Initialization

This area is only applicable to migration tasks that start from the assessment stage, and is used to configure whether to create source tablespaces that do not exist on the target side before migration. The default is **[ skip ]**, meaning that tablespaces will not be created before migration.

When choosing **[ not skip ]**, you will need to continue to configure the source tablespace information displayed in the box on the page:

- The page will list the source tablespace information within the migration scope, including the number of tablespaces, total data volume, name, size, and used size of each tablespace.

- For the tablespaces you wish to create, you can edit the parameter values of the data files used for tablespace initialization and click to save for them to take effect. If the input values do not meet the target table requirements, the system will adjust them to compatible values for the target side.

  For example, YashanDB stipulates that each data file requires a minimum of 128 blocks of storage units, so when DB_BLOCK_SIZE is set to 8K, the minimum data file size must be 1M. If the user inputs a value of 104KB for the data file size, the system will adjust it and prompt.

- When the default table type on the target side is LSC, you can input the data bucket path information used for tablespace initialization.


