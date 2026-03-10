## Default Built-in Database Installation of YMP

### Step 1: Modify Configuration as Needed

Upload the YMP installation package to the /home/ymp directory and then unzip it:

```shell
# Switch to YMP installation directory
$ cd /home/ymp/
$ unzip yashan-migrate-platform-xx.x.x.x-linux-xxx.zip
```

By default, YMP uses the yashan mode to install the built-in database. If you want to use the mysql mode, please modify the YASDB_MODE parameter in the configuration file conf/db.properties.

If there are other modifications, please adjust the installation of the default built-in database and the YMP startup [parameters](../../Refrence/Parameter Explanation) according to actual needs.

### Step 2: Execute Installation

YMP installs YashanDB as the built-in database by default. Related information about the default built-in database:

|Deployment Type |Database Name |Installation Directory |Supported Major Database Versions |
| ---- | ----- | ---------------------------------------------------------------- | ------------------------ |
| Standalone       | ymp            | /home/ymp/yashan-migrate-platform/db                                   | 22.2, 23.1, 23.2, 23.3, 23.4      |

If you need to change the default password of the SYS user for the built-in database, please note: the password change operation is only effective before the installation deployment, and must meet the [password requirements](../../Refrence/Password).

You can check the script parameters and their usage methods with the following command:

```shell
# Enter the installation directory to see help
$ cd /home/ymp/yashan-migrate-platform/
$ sh bin/ymp.sh -h

# Enter the installation directory to execute the installation command
$ cd /home/ymp/yashan-migrate-platform/
$ sh bin/ymp.sh install --db /home/ymp/yashandb-23.2.1.0-linux-x86_64.tar.gz
```

### Step 3: Check Running Status and Version

```shell
 $ cd /home/ymp/yashan-migrate-platform/
# Check running status
 $ sh bin/ymp.sh status  
 YMP is running, pid is 7747.
 Built-in database is used, pid is 7617.
# Check version
 $ sh bin/ymp.sh -v 
 Yashan-migrate-platform version: Release v23.2.1.0
 YashanDB SQL Enterprise Edition Release v23.2.1.0 x86_64
 YashanDB Loader Enterprise Edition Release v23.2.1.0 x86_64 faec879
```

### Step 4: Access YMP

Access method: http://IP:PORT/, PORT defaults to 8090, initial username and password are (admin/admin).

<span id="StopYMP" name="StopYMP" class="yaslink"></span>

## Stop YMP

Start YMP for Built-in Database:

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh start
```

Stop YMP for Built-in Database:

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh stop
```

Restart YMP for Built-in Database:

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh restart
```

> **Caution**:
> 
> Stopping or restarting YMP during task execution may cause the current phase of the task to fail and require restarting the current phase task.