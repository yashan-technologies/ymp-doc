

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



- Table Name Check

  - Pre-check conditions: None.

  - Pre-check content: The table name check will verify whether there are case-insensitive duplicate tables on the source side.

  - Pre-check results: If there are case-insensitive duplicate tables on the source side, the page will display **[ warning ]** and specific alert information, but it will not block the next data migration operation.

- Type Check

  - Pre-check conditions:

    * When **[ Full data migration ]** is selected, a common type check for tables will be performed, assessing the risk of field types that may cause exceptions during the data migration process.
    * When **[ Object migration ]** is selected, a common type check for tables will be performed, assessing field types that may pose risks due to semantic differences in heterogeneous databases after migration.
    * When both **[ Object migration ]** is selected and the object conflict strategy is set to **[ overwrite ]**, a UDT check will be performed.

  - Pre-check content: The type check includes common type checks for tables and UDT checks. The UDT check will verify if the migrated UDT exists on the target side and is referenced. The following table lists the types checked when the source side is MySQL:

    |Data Type |Risk Reason |
    | ---------- | ------------------------------------------------------------ |
    | VARCHAR    | The support range differs between MySQL and YashanDB       |
    | DECIMAL    | DECIMAL migrating to YashanDB becomes NUMBER; precision ranges differ between MySQL and YashanDB |
    | JSON       | Maximum size differs between MySQL and YashanDB            |
    | SET        | SET migrates to YashanDB as VARCHAR; support ranges differ between MySQL and YashanDB |
    | BINARY     | If this type of field is defined with a default value, the default value of MySQL has semantic differences with YashanDB, requiring conversion to hexadecimal; otherwise, it may pose risks after migration. |
    | VARBINARY  | If this type of field is defined with a default value, the default value of MySQL has semantic differences with YashanDB, requiring conversion to hexadecimal; otherwise, it may pose risks after migration. |
    | LONGBLOB   | If this type of field is defined with a default value, the default value of MySQL has semantic differences with YashanDB, requiring conversion to hexadecimal; otherwise, it may pose risks after migration. |
    | MEDIUMBLOB | If this type of field is defined with a default value, the default value of MySQL has semantic differences with YashanDB, requiring conversion to hexadecimal; otherwise, it may pose risks after migration. |
    | BLOB       | If this type of field is defined with a default value, the default value of MySQL has semantic differences with YashanDB, requiring conversion to hexadecimal; otherwise, it may pose risks after migration. |
    | TINYBLOB   | If this type of field is defined with a default value, the default value of MySQL has semantic differences with YashanDB, requiring conversion to hexadecimal; otherwise, it may pose risks after migration. |

  - Pre-check results:

    * If there are any of the above data types, the page will display **[ warning ]** and specific alert information, but it will not block the next data migration operation.
    * If the migrated UDT is referenced on the target side, the page will display **[ warning ]** and specific alert information, but it will not block the next data migration operation.

- Character Set Check 

  - Pre-check content: The character set check requires compatibility between the source and target character sets to avoid issues such as data garbling during migration.  
  - Pre-check results: If the character set of the source side is incompatible with that of the target side, the page will display **[ warning ]** and specific alert information, but it will not block the next data migration operation.
  
  According to the character sets supported by YashanDB (GB18030, GBK, UTF8, ASCII, and ISO-8859-1), the character set compatibility rules are as follows:
  
    | YashanDB   | MySQL                |
    | ---------- | -------------------- |
    | GB18030    | gbk、gb2312、gb18030 |
    | GBK        | gbk、gb2312          |
    | UTF8       | utf8、utf8mb4        |
    | ASCII      | ascii                |
    | ISO-8859-1 | latin1               |
  
- Other Checks

  - Pre-check content: Other checks include active scheduled task checks and materialized view checks in non-auto-refresh mode. For MySQL, scheduled tasks are Events.
  - Pre-check results: If there are active scheduled tasks on the source side, the page will display **[ warning ]** and specific alert information, but it will not block the next data migration operation. If there are materialized views in a non-ON COMMIT refresh mode on the source side, the page will display **[ warning ]** and specific alert information, but it will not block the next data migration operation.
