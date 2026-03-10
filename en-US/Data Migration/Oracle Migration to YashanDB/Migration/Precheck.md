

For migration tasks that start from the assessment phase, the pre-check includes connection checks, privilege checks, capacity checks, deployment method checks, version checks, default table type checks, tablespace checks, type checks, table index checks, character set checks, and other checks.

For tasks that start migration directly without the assessment phase, the pre-check includes connection checks, privilege checks, capacity checks, version checks, type checks, table index checks, character set checks, object changes checks, and other checks.

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

  - Pre-check content: The source and target database users required for the privilege check must have sufficient privileges.

    If the migration scope includes **[ Object migration ]** or **[ Supplement object integrity ]**, a privilege check will be performed to verify whether the target database has a separation of concerns. Automatic permission assignment is not possible; the user must manually address privilege issues. For details, please refer to the [separation of concerns privilege migration issues](../../../FAQ/Migration Task.html#problem40).

  - Pre-check result: If the check fails, the page will display **[ warning ]** and specific error messages, but this will not block the next step of the data migration operation.



- Assessment & Target Check
  - Pre-check Condition: This check will be performed when assessment is selected.
  - Pre-check Content:
    - USE_NATIVE_TYPE is a configuration parameter for database native data types with a default value of TRUE (enables native data types) and FALSE (enables Oracle-compatible types); the parameter check requires the USE_NATIVE_TYPE configurations of the evaluation database and target database to be consistent.
    - The deployment method check requires that the assessment database and target database have the same deployment method: Standalone or YAC Deployment.
    - The default table type check requires that the assessment database and target database have the same default table types: HEAP, LSC, or TAC, which can be queried using `SELECT DEFAULT_TABLE_TYPE FROM V$PARAMETER`.
    - Version check requires the version numbers of the evaluation database and target database to be exactly consistent, which can be queried via the command: `SELECT VERSION_NUMBER FROM V$VERSION`.
  - Pre-check Result: If the check fails, the page will display **[ warning ]** along with specific error messages, without blocking the next data migration operation.



- Version Check

  - Pre-check conditions: When access is selected and **[Full Data Migration]** is checked, a version check of yasldr, the target database and OCI will be performed.

  - Pre-check content: The version check will include the following:

    - The yasldr and target database version check will compare the version numbers of yasldr and the target database. The yasldr version can be queried using `ymp.sh -v`.
    - The OCI version check will compare the current OCI client version with the expected version number.
    
  - Pre-check result:
    
    The yasldr and target database version check is based on the following rules:
    
    * If the version numbers of yasldr and the target database do not match in all digits, the page will display **[ warning ]** and specific warning messages, but this will not block the next step of the data migration operation.
    * If the version numbers of yasldr and the target database match in all digits, the page will display **[ check passed ]**.
    
    The OCI version check is based on the following rules:
    
    - If the current OCI client version is empty or does not fully match the expected version number, the page will display **[ warning ]** and specific warning messages, but this will not block the next step of the data migration operation.
    - If the current OCI client version fully matches the expected version number, the page will display **[ check passed ]**.
  
- Type Check

  - Pre-check conditions:

    * When **[ Full data migration ]** is checked, a standard type check on tables will be performed, focusing on field types that may cause exceptions during the data migration process.
    * When **[ Object migration ]** is checked, a standard type check on tables will be performed, focusing on field types that may present risks due to semantic discrepancies in heterogeneous databases after migration.
    * When **[ Supplement object integrity ]** is checked and the object conflict policy is set to **[ overwrite ]**, a UDT check will be conducted.

  - Pre-check content: The type check includes standard type checks on tables and UDT checks. The UDT check will verify whether the migrated UDT exists in the target and is referenced. The table below lists the data types checked when the source is Oracle:

    |Data Type |Risk Reason |
    | ------------------------------ | ------------------------------------------------------------ |
    | VARCHAR2, NVARCHAR2            | The support range differs between Oracle and YashanDB        |
    | JSON                           | The maximum size differs between Oracle and YashanDB        |
    | INTERVAL DAY TO SECOND         | The support range differs between Oracle and YashanDB        |
    | DATE                           | The support range differs between Oracle and YashanDB        |
    | TIMESTAMP                      | The support range and precision differ between Oracle and YashanDB |
    | TIMESTAMP WITH TIME ZONE       | The support range and precision differ between Oracle and YashanDB, and time zone information may be lost |
    | TIMESTAMP WITH LOCAL TIME ZONE | The support range and precision differ between Oracle and YashanDB, and time zone information may be lost. If YMP and Oracle time zones are inconsistent, data may be inconsistent |
    | ROWID                          | The ROWID type in Oracle is incompatible with YashanDB, posing risks for usage after migration |
    | UROWID                         | The UROWID type in Oracle is incompatible with YashanDB, posing risks for usage after migration |
    
  - Pre-check result:
  
    * If any of the above data types are present, the page will display **[ warning ]** and specific warning messages, but this will not block the next step of the data migration operation.
    * If the migrated UDT is referenced in the target, the page will display **[ warning ]** and specific warning messages, but this will not block the next step of the data migration operation.
  
- Character Set Check

  - Pre-check content: The character set check requires compatibility between the source and target character sets to avoid issues such as data corruption during migration.
  - Pre-check result: If the source character set is not compatible with the target character set, the page will display **[ warning ]** and specific warning messages, but this will not block the next step of the data migration operation.

  Based on the character sets supported by YashanDB (GB18030, GBK, UTF8, ASCII, and ISO-8859-1), the compatibility rules for character sets are as follows:

  | YashanDB   | Oracle                 |
  | ---------- | ---------------------- |
  | GB18030    | ZHS16GBK、ZHS32GB18030 |
  | GBK        | ZHS16GBK               |
  | UTF8       | AL32UTF8、UTF8         |
  | ASCII      | US7ASCII               |
  | ISO-8859-1 | WE8ISO8859P1           |

- Object Changes Check

  - Pre-check conditions: This check is triggered when any of the following combinations are checked:
    - **[Object migration]**+**[Incremental data migration]**
    - **[Supplement object integrity]** + **[Incremental data migration]**
    - **[Object migration]**+**[Supplement object integrity]**+**[Incremental data migration]**
  - Pre-check content: Remind users to check if the status of migration objects has changed.
  - Pre-check result: The page displays a **[Warning]** and specific warning information, but does not block the next data migration operation.

- Other Checks

  - Pre-check content: Other checks include checking for active scheduled tasks and materialized views that are not in auto-refresh mode. For Oracle, scheduled tasks refer to Jobs/Schedulers. The check for non-auto-refresh mode materialized views requires the absence of materialized views not set to refresh ON COMMIT on the source side.

  - Pre-check result: If there are active scheduled tasks on the source side, the page will display **[ warning ]** and specific warning messages, but this will not block the next step of the data migration operation. If there are materialized views not set to refresh ON COMMIT on the source side, the page will display **[ warning ]** and specific warning messages, but this will not block the next step of the data migration operation.
