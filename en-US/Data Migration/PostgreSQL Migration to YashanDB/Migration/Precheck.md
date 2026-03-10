

For migration tasks that start from the assessment phase, the pre-check includes connection checks, privilege checks, capacity checks, deployment method checks, version checks, default table type checks, tablespace checks, type checks, table index checks, character set checks, and other checks.

For tasks that start migration directly without the assessment phase, the pre-check includes connection checks, privilege checks, capacity checks, version checks, type checks, table index checks, character set checks, and other checks.

Each check is optional. Users can skip checks after assessing the risks.

- Connection Check

  - Pre-check Content: The connection check will verify the connectivity status of the source and target databases. Both source and target databases must be in an available state and have operational privileges.

  - Pre-check Result: If the check fails, the page will display **[ warning ]** along with specific error messages, without blocking the next data migration operation.

- Capacity Check
  - Pre-check Condition: This check will be performed when **[ data migration ]** is selected.

  - Pre-check Content: The capacity check will determine whether the file storage path for the data migration task (`${ymp.home}/tmp/`) has sufficient space.

  - Pre-check Result: **[ warning ]**: If the available disk space for migration (80% of the available space) in the path is less than one times the maximum single-table data size in the tables to be synchronized, the page will display **[ warning ]** along with specific alert messages, without blocking the next data migration operation. **[ check passed ]**: If the available disk space for migration is not less than one times the maximum single-table data size, the page will display **[ check passed ]**.

- Tablespace Check

  - Pre-check Condition: This check will only be performed when assessment is selected. Additionally, the tablespace capacity check must also have **[ Full data migration ]** checked, and the LSC tablespace check requires the default table type to be LSC.

  - Pre-check Content: The tablespace check includes checks for the target-side tablespace, tablespace capacity, and LSC tablespace. The tablespace check will include the following:

    - When the tablespace initialization is set to **[ not skip ]**, the system will verify the presence of the target-side tablespace information and compare results with the configuration.
    - When the tablespace initialization is set to **[ skip ]**, it will check whether the required tablespace exists on the target side.
    - It will check whether the capacity of the existing target-side tablespace meets the data migration requirements and whether the data bucket is mounted.

  - Pre-check Result:
    The target-side tablespace check is based on the following rules:

    - When the tablespace initialization is not skipped, if the existence status of the target-side tablespace differs from the configuration, the page will display **[ warning ]** along with specific error messages, without blocking the next data migration operation. This indicates that users need to pay attention to the expired configuration of tablespace initialization, which is inconsistent with the actual existence on the target side and requires re-obtaining and configuring the tablespace.

    - When the tablespace initialization is skipped, if the necessary tablespace for migration does not exist on the target-side tablespace, the page will display **[ warning ]** along with specific error messages, without blocking the next data migration operation.

    The tablespace capacity check is based on the following rules:

    * When the target-side tablespace files have auto-extension enabled, if the total maximum size of the tablespace files is less than the used size of the source-side tablespace files, the page will display **[ warning ]** along with specific alert messages, but will not block the next data migration operation.

    * When the target-side tablespace files have no auto-extension enabled, if the total size of the tablespace files is less than the used size of the source-side tablespace files, the page will display **[ warning ]** along with specific alert messages, but will not block the next data migration operation.

    * If the existing target-side tablespace has not mounted the data bucket, the page will display **[ warning ]** along with specific alert messages, but will not block the next data migration operation. Users can mount the data bucket themselves (using `ALTER TABLESPACE tablesapce_name ADD DATABUCKET 'bucket1','bucket2'` for mounting; refer to the section on ALTER TABLESPACE in the Yashan Database documentation for specific syntax).

- Table Index Check

  - Pre-check Condition:
    - When **[ Full data migration ]** is checked, it will check whether indexes already exist on the target table.
    - When both **[Object Migration]** and **[Full Data Migration]** are checked, if the object conflict strategy is "overwrite", deleting a table will synchronously delete the indexes, so this check is not required.
  - Pre-check Content: Check whether indexes already exist on the target table.
  - Pre-check Result:
    - If indexes exist, the page will display **[ warning ]** along with specific alert messages but will not block the next data migration operation.
    - Users can download a file of the number of indexes counted for details.





- Privilege Check
  - Pre-check Content: The privilege check requires the source and target database users used to have sufficient privileges.
  - Pre-check Result: If the check fails, the page will display **[ warning ]** along with specific error messages, without blocking the next data migration operation.



- Assessment & Target Check
  - Pre-check Condition: This check will be performed when assessment is selected.
  - Pre-check Content:
    - USE_NATIVE_TYPE is a configuration parameter for database native data types with a default value of TRUE (enables native data types) and FALSE (enables Oracle-compatible types); the parameter check requires the USE_NATIVE_TYPE configurations of the evaluation database and target database to be consistent.
    - The deployment method check requires that the assessment database and target database have the same deployment method: Standalone or YAC Deployment.
    - The default table type check requires that the assessment database and target database have the same default table types: HEAP, LSC, or TAC, which can be queried using `SELECT DEFAULT_TABLE_TYPE FROM V$PARAMETER`.
    - Version check requires the version numbers of the evaluation database and target database to be exactly consistent, which can be queried via the command: `SELECT VERSION_NUMBER FROM V$VERSION`.
  - Pre-check Result: If the check fails, the page will display **[ warning ]** along with specific error messages, without blocking the next data migration operation.



- Version Check

  - Pre-check Condition: When assess is selected and **[Full Data Migration]** is checked, a version check of yasldr and the target database will be performed.
  - Pre-check Content: Version check requires the version numbers of yasldr and the target database to match, and the yasldr version can be queried via the command: `ymp.sh -v`.
  - Pre-check Result: If the check fails, the page will display **[ warning ]** along with specific error messages, without blocking the next data migration operation.





- Type Check
  - Pre-check Condition:

    * When **[ Full data migration ]** is checked, it will perform a normal type check on the table to find risk fields that may cause exceptions during data migration.
    * When **[ Object migration ]** is checked, it will perform a normal type check on the table to identify field types that may pose risks due to semantic differences in heterogeneous databases after migration.
    * When **[ Object migration ]** is checked and the object conflict policy is set to **[ overwrite ]**, it will perform a UDT check.

  - Pre-check Content: The type check includes both normal type checks and UDT checks. The UDT check will verify whether the migrated UDT exists on the target side and is being referenced.

  - Pre-check Result:

    * If any of the above data types exist, the page will display **[ warning ]** along with specific alert messages, but will not block the next data migration operation.
    * If the migrated UDT is referenced on the target side, the page will display **[ warning ]** along with specific alert messages, but will not block the next data migration operation.

- Character Set Check

  - Pre-check Content: The character set check requires that the source and target character sets are compatible to avoid issues like garbled data during migration.

  - Pre-check Result: If the source character set is not compatible with the target character set, the page will display **[ warning ]** along with specific alert messages, but will not block the next data migration operation.
  
- Other Checks

  - Pre-check Content: Other checks include active scheduled task checks and checks for materialized views with non-auto-refresh modes. The active scheduled task check requires that there are no active scheduled tasks on the source side (excluding database-provided scheduled tasks).

  - Pre-check results: If there are active scheduled tasks on the source side, page F will display a **[Warning]** along with specific alarm information, but it will not block the subsequent data migration operation. If there are materialized views in non-ON COMMIT refresh mode on the source side, the page will display a **[Warning]** along with specific alarm information, but it will not block the subsequent data migration operation.


