## Upgrade Instructions

The upgrade functionality provides an upgrade from the old version of YMP to the new version of YMP and normal usage, but some functionalities are limited. It is recommended that users first use the normal [uninstall](Installing and Uninstalling/Uninstall) functionality of YMP.

The versions supported for upgrade are: (~ indicates inclusion of all intermediate versions)

|Old YMP Version |New YMP Version |Upgrade Restrictions |Solutions |
|-----------|-----------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| 23.1.1.0        | 23.2.1.2        | The total amount of data in the schema in the evaluation result is 0                                        | Full refresh report or re-evaluate          |
|                 |                 | The number of derived objects, the proportion of compatible derived objects, and the proportion of incompatible derived objects in the evaluation result are all 0  | Re-evaluate                                 |
|                 |                 | Only configurations that exist in the new version of YMP are updated, configurations not mentioned in the documentation are not upgraded and synchronized   | Manually add after upgrade                  |
|                 |                 | The migration report cannot reflect the conversion rules at the time of evaluation                          | Re-evaluate                                 |
|                 |                 | If modifications have been saved to the DDL of tables that will generate derived objects (such as sequences, triggers) before the upgrade, and after the upgrade, the manual DDL retention policy for re-evaluation has changed to **[full retention]**, besides an additional derived object, the derived object DDL in the table still exists, which may lead to migration failure | Do not migrate failed derived objects during data migration, it will not affect migration results |
| 23.2.1.2        | 23.2.1.3        | The new version starts supporting role migration after the upgrade, and the role initialization information in the migration configuration is empty              | Manually refresh the role list              |
| 23.2.1.3~       | ~23.2.1.5       | None                                                                                                       |                                             |
| 23.2.1.5        | 23.2.1.6        | The status of the verification report will change to generation failure after the upgrade                   | Regenerate the verification report           |
| 23.2.1.6~       | ~23.3.1.2       | None                                                                                                       |                                             |
| 23.3.1.2        | 23.3.2.3        | None                                                                                                       |                                             |
| 23.3.2.5        | 23.3.2.7        | Cannot view and download the migration logs and verification logs generated before the upgrade              | Search using task names from YMP installation logs migration/ and ydc_log/ |
| 23.3.2.7        | 23.3.2.9        | Cannot use new features: unsupported recovery of failed evaluation tasks and unsupported refresh of compatibility rates for individual objects  | Can re-evaluate or use after full refresh report |
| 23.3.2.9~       | ~23.4.1.3       | None                                                                                                       |                                             |
| 23.4.1.3        | 23.4.3.2        | The YMP_DEFAULT derived data source created with MySQL mode before the upgrade is temporarily unavailable  | Test the connection again before use and ensure it is saved |
|                 |                 | Modifying the syntax mode configuration of the built-in library (YASDB_MODE in conf/db.properties) is not supported before the upgrade; it defaults to yashan mode | Use an external library in MySQL mode if needed |
| 23.4.3.2~       | ~23.5.7.2      | Switching the syntax mode of the built-in library is not supported before and after the upgrade             |                                             |

> **Note**:
> 
> 1. The supported versions in the table can be upgraded across versions, and the functionality limitations for cross-version upgrades are cumulative across the various versions.
> 2. For custom built-in database start of old version YMP before 23.2.1.4, it is necessary to manually create a YMP_DEFAULT_UPGRADE user and grant permissions in the custom database before upgrading, with the password consistent with the old version business user YMP_DEFAULT.
> 3. Starting from 23.2.1.4, multiple upgrades are supported. For example, upgrading from 23.1.1.0 to 23.2.1.2 and then using it for a period of time before upgrading to 23.2.1.4.
> 4. When performing multiple upgrades using 23.1.1.0 as the baseline version, it is necessary to manually change the OCI version after each upgrade (refer to [Replacing Components](Replacing Components)).
> 5. From version 23.3.1.2 onwards, only upgrades between release versions (xx.x.2.x as release versions) are supported, while xx.x.1.x are beta versions and upgrading is not supported.

## Environment Conditions

- The bin/operate.log log cannot be cleared under the old version of YMP.
- Before upgrading, ensure that the db and bin/yasldr directories under the new version YMP path are empty.
- The ymps.env file from the previous version must exist in the ~/.yasboot directory.

## Pre-Upgrade Preparation

### Upload the New YMP Installation Package

```shell
# Create a new version YMP installation address
$ mkdir /home/ymp/upgrade
# Switch to YMP upgrade directory and unzip
$ cd /home/ymp/upgrade
$ unzip yashan-migrate-platform-x86-64.zip
```

### Backup ymps.env File

To avoid the situation where the built-in library of the old version YMP cannot be started due to accidentally deleting the backup file after a failed upgrade, it is recommended to manually back up the ~/.yasboot/ymp.env file before starting any updates.

```shell
$ cp ~/.yasboot/ymp.env ~/.yasboot/ymp.env_bak
```

### Stop the Old Version YMP

```shell
# Switch to the old version YMP directory and stop
$ cd /home/ymp/yashan-migrate-platform
```

#### Old Version YMP Using Default Built-in Database

```shell
# Stop YMP
$ sh bin/ymp.sh stop
# Record the old version path, needed during the upgrade
$ pwd
```

#### Old Version YMP Using Custom Built-in Database

```shell
# Stop YMP
$ sh bin/ymp.sh stopnodb
# Record the old version path, needed during the upgrade
$ pwd
```

> **Note**：
> 
> Only stop YMP, do not uninstall. The uninstall operation cannot be executed, and there should be no changes to the installation packages that the old version YMP depends on.

## Upgrade YMP

### Switch to New Version YMP Directory

```shell
# Switch to the new version YMP upgrade directory
$ cd /home/ymp/upgrade/yashan-migrate-platform
```

### Use Upgrade Command to Upgrade YMP

```shell
# Before upgrading, ensure that the db and bin/yasldr directories under the new version YMP path are empty
$ sh bin/ymp.sh upgrade --from /home/ymp/yashan-migrate-platform --db /home/ymp/yashandb-23.2.1.0-linux-x86_64.tar.gz

# --db parameters are optional; if not specified, it will automatically complete the old version YMP's path using the operate.log.
```

Wait for the upgrade to complete. After a successful upgrade, the new version of YMP will automatically start without manual initiation.

### Successfully Upgraded

After completion, the business data will be retained under the (default/custom) built-in library user YMP_DEFAULT_{version}_{upgrade_time} (for example: YMP_DEFAULT_V23214_20240531175950), with the password remaining consistent with the user prior to the upgrade.

You can restart the browser and normally use the new version YMP functionality.

If a rollback to the old version is required after a successful upgrade, you can switch back to the old version using the following operations.

1. Stop the new version YMP
   
   ```shell
   $ sh bin/ymp.sh stop
   ```

2. Backup the new version ymps.env file
   
   ```shell
   $ cp ~/.yasboot/ymp.env cp ~/.yasboot/new_ymp_bak.env
   ```

3. Delete the new version ymps.env file
   
   ```shell
   $ rm -rf ~/.yasboot/ymp.env
   ```

4. If ymps_yasdb_home exists, delete it directly
   
   ```shell
   $ rm -rf ~/.yasboot/ymp_yasdb_home
   ```

5. Manually restore the old version ymps.env file (for example, if the old version is 23.1.1.0):
   a. If the backup file still exists under tmp/upgrade:
   
   ```shell
   $ cp tmp/upgrade/ymp-v23.1.1.0.env ~/.yasboot/ymp.env
   ```

   b. If the backup file under tmp/upgrade has been deleted, restore using the manual backup file.
   
   ```shell
   $ cp ~/.yasboot/ymp.env_bak ~/.yasboot/ymp.env
   ```

6. If the built-in library used by the old version YMP needs ymps_yasdb_home, re-establish the symbolic link (for example, using version 23.4.1.100 database):
   
   1. Check if the database version number file exists in /home/ymp/yashan-migrate-platform/db/, for example: /home/ymp/yashan-migrate-platform/db/23.4.1.100. If the folder exists, it indicates a symbolic link is needed, please continue to the next step.
   
   2. Re-establish the symbolic link:
      
      ```shell
      $ ln -s /home/ymp/yashan-migrate-platform/db/23.4.1.100 ~/.yasboot/ymp_yasdb_home
      ```

7. Switch to the old version YMP directory and start

```shell
$ cd /home/ymp/yashan-migrate-platform
$ sh bin/ymp.sh start or sh bin/ymp.sh startnodb
```

### Upgrade Failure

If the upgrade fails, you can switch to the old version of YMP path and launch the old version YMP to continue usage.

1. Manually restore the old version ymps.env file, with 23.1.1.0 as the example:

```shell
$ cp tmp/upgrade/ymp-v23.1.1.0.env ~/.yasboot/ymp.env
```

2. If the backup file in tmp/upgrade has been deleted, restore using your manual backup file

```shell
$ cp ~/.yasboot/ymp.env_bak ~/.yasboot/ymp.env
```

3. If ymps_yasdb_home exists, delete and recreate it, using version 23.4.1.100 database as an example:
   
   ```shell
   $ rm -rf ~/.yasboot/ymp_yasdb_home
   $ ln -s /home/ymp/yashan-migrate-platform/db/23.4.1.100 ~/.yasboot/ymp_yasdb_home
   ```

4. Switch to the old version YMP directory and start
   
   ```shell
   $ cd /home/ymp/yashan-migrate-platform
   $ sh bin/ymp.sh start or sh bin/ymp.sh startnodb
   ```
