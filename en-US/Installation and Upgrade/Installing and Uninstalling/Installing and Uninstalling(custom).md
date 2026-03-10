## Installing YMP with Custom Built-in Database

Using a custom built-in database means using an external YashanDB database as the business database for YMP, and the YashanDB database will not be installed during the YMP installation.

**The migrated target database cannot be used as the Custom Built-in Database.**

### Step 1: Modify Configuration as Needed

Upload the YMP installation package to the /home/ymp directory and then unzip it:

```shell
# Switch to the YMP installation directory
$ cd /home/ymp/
$ unzip yashan-migrate-platform-xx.x.x.x-linux-xxx.zip
```

Modify the configuration file:

```shell
# Modify the yashan-migration-platform/conf/application.properties file

# Enter the installation directory
$ cd /home/ymp/yashan-migrate-platform/

# Execute the command to change connection information, specifying the url for the Custom Built-in Database, formatted as IP:PORT; the default username and password do not need to be specified, which are "YMP_DEFAULT" and "Ymppw602."
$ sh bin/ymp.sh connection --url 192.168.x.x:xxxx

# If you need to change user information when specifying a custom database (for example, with password ymp_new for user YMP_NEW), refer to the following command:
$ sh bin/ymp.sh connection --url 192.168.x.x:xxxx --username YMP_NEW --password ymp_new
```

### Step 2: Execute Installation

**Method 1: Install yasldr dependency library and start YMP**

If you do not need to use the Oracle to YashanDB data migration functionality, you can deploy it this way.

```shell
# Enter the installation directory and execute the uninstall command
$ cd /home/ymp/yashan-migrate-platform/
$ sh bin/ymp.sh installnodb --db /home/ymp/yashandb-23.2.1.0-linux-x86_64.tar.gz
```

**Method 2: Install yasldr dependency library and start YMP (optional solution with Oracle as Data Source)**

```shell
# Enter the installation directory and execute the installation command
$ cd /home/ymp/yashan-migrate-platform/
$ sh bin/ymp.sh installnodb --db /home/ymp/yashandb-23.2.1.0-linux-x86_64.tar.gz
```

### Step 3: Check Running Status and Version

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 # Check running status
 $ sh bin/ymp.sh status
 YMP is running, pid is 24116.
 Built-in database is not used!
 
 $ sh bin/ymp.sh -v
 Yashan-migrate-platform version: Release v23.2.1.0
 YashanDB SQL Enterprise Edition Release v23.2.1.0 x86_64
 YashanDB Loader Enterprise Edition Release v23.2.1.0 x86_64 faec879
```

### Step 4: Access YMP

Access method: http://IP:PORT/, PORT defaults to 8090, initial username and password are (admin/admin).

## Starting and Stopping YMP

Start YMP with Custom built-in database:

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh startnodb
```

Stop YMP with Custom built-in database:

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh stopnodb
```

Restart YMP with Custom built-in database:

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh restartnodb
```

> **Caution**:
> 
> Stopping or restarting YMP during task execution will cause the current task to fail, requiring the current task to be restarted.