## Server Preparation

### Server Configuration

|Configuration Item |Minimum Requirements |Description |
|:------ |:--------------------------|:------------------------------------------------------------------- |
| Operating System   | CentOS 7.6 or above, KylinOS V10, RHEL 9.3 |                                                                     |
| Processor Architecture  | X86-64, ARM-64             | For ARM-64 architecture, the required JDK version for YMP must be JDK17, which can effectively resolve potential database connection slow issues.          |
| CPU Cores  | 4 cores or more                     |                                                                     |
| Available Memory   | 8G or more                     | Memory usage varies based on the complexity of the database model; configuration reference: [Common Migration Issues 22](../../FAQ/Migration Task.html#problem22) |
| Disk Available Space | SSD, size based on the size of the migration table         | It is recommended to be no less than three times the maximum data volume of a single table in the table to be migrated                                               |

> **Note**:
> 
> The deployment environment configuration must meet at least the minimum requirements. If the configuration does not meet the minimum requirements, although configuration parameters may be modified to run the YMP system, issues like lag and memory overflow may occur during task usage.

### Operating System Parameter Adjustment

To ensure the normal installation and operation of the default built-in database YashanDB in YMP, it is recommended to log in to the server as root or a user with sudo privileges to make configuration adjustments as listed in this document.

Adjust some resource limit values (view all resource limit values using `ulimit -a`) to recommended values or above, with detailed information as shown in the table below, where `ymp` is an example of the planned YMP installation user (the specific username should be based on actual planning).

|Resource Item |Recommended Value |OS Command |Description |
| :----------------- | :-------- | :----------------------------------------------------------- | :----------------- |
| open files         | 1048576   | `ulimit -n 1048576` <br/> Add to `vi /etc/security/limits.conf`: <br/>`ymp soft nofile 1048576` <br/> `ymp hard nofile 1048576` | Number of open files in the process   |
| max user processes | 1048576   | `ulimit -u 1048576` <br/> Add to `vi /etc/security/limits.conf`: <br/> `ymp soft nproc 1048576` <br/> `ymp hard nproc 1048576` | Maximum number of user processes     |
| max memory size    | unlimited | `ulimit -m unlimited` <br/> Add to `vi /etc/security/limits.conf`: <br/> `ymp soft rss unlimited` <br/> `ymp hard rss unlimited` | Maximum memory usage of the process |
| stack size         | 8192      | `ulimit -s 8192` <br/> Add to `vi /etc/security/limits.conf`: <br/> `ymp soft stack 8192` <br/> `ymp hard stack 8192` | Maximum stack space of the process     |

### Open Ports

**Default Installation Port Numbers**

Refer to the port list: [Port List](../../Refrence/Port List).

|YMP Listening |Database Listening |Inter-Host Communication |yasom |yasagent |
| ----- | ----- | ----- | ----- | -------- |
| 8090  | 8091  | 8092  | 8093  | 8094     |

**Method 1: Disable Firewall**

Run the following command on the server to disable the firewall:

```shell
# Disable firewall
systemctl stop firewalld 

# Disable boot auto-start
systemctl disable firewalld
```

**Method 2: Add to Whitelist**

If the firewall cannot be disabled, follow the steps below to add ports to the whitelist:

1. Check the open ports in the firewall.

```shell
firewall-cmd --zone=public --list-ports
```

2. Add ports to the firewall.

Here, using port 8090 as an example to demonstrate how to add a port to the firewall; other port operations are similar.

```shell
# Add --permanent for permanent effect; without this parameter, it will be ineffective after reboot
firewall-cmd --zone=public --add-port=8090/tcp --permanent

# Reload
firewall-cmd --reload

# Check
firewall-cmd --zone=public --query-port=8090/tcp
```

To remove an already added port from the whitelist, use the following command:

```shell
# Remove the added port
firewall-cmd --zone=public --remove-port=8090/tcp --permanent
```

## User Preparation

<span id="UserPreparation" class="yaslink"></span>

It is recommended to create a new user on the server for installing and deploying YMP; note that the YMP installation path must not contain English periods.

Create a user for installing YMP, using `ymp` as an example. After user creation and authorization, all subsequent installation steps will be performed under this user.

```shell
# Create YMP user
useradd -d /home/ymp -m ymp
passwd ymp
```

## Application Environment Preparation

### Server Tool Preparation

```shell
# Install lsof command tool
yum install -y lsof
```

### JDK Environment Preparation

Refer to the JDK version requirements for YMP: [Supported JDK](#supportedDependenceVersion). After downloading the specified version of the JDK from the official Java path and successful installation, configure the following environment variables:

```shell
# For example, if the JDK17 installation path for user ymp is /usr/tools/jdk17
# su - ymp 
$ vi ~/.bash_profile

# Add the following content at the end of the file
export JAVA_HOME=/usr/tools/jdk17
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# Reload configuration file
$ source ~/.bash_profile

# After successful installation, check the JDK version information
$ java -version
```

### libaio Environment Preparation

YMP requires the libaio dynamic library to run.

```shell
# Check if the libaio dynamic library is already installed
rpm -qa | grep libaio

# If no version information is printed, install libaio
yum install -y libaio
```

### YashanDB Environment Preparation (Optional)

This step can be skipped when using the default built-in database.

When using a custom built-in database:

* If a new YashanDB Standalone Deployment is needed, refer to the YashanDB official documentation for installation and deployment.

* For an existing YashanDB Standalone Deployment, the DBA must execute the following script in that environment:

```sql
 -- Create a user named ymp (e.g., YMP_DEFAULT) and authorize it
 CREATE USER YMP_DEFAULT IDENTIFIED BY Ymppw602. DEFAULT TABLESPACE users;
 GRANT ALL PRIVILEGES TO YMP_DEFAULT;
 GRANT DBA TO YMP_DEFAULT;
```

### DB2 Client Environment Preparation (Optional)

If you need to use DB2 to YashanDB evaluation functionality, please install the DB2 client environment:

1. Self-install using the DB2 client package.

2. Use the [deploy command](../../Refrence/Command Description.html#db2_deploy) in ymp.sh to start the visual deployment page. Ensure that the deployment environment supports visual operation before using this command.

3. When using DB2 as the source, YMP should be deployed under the user of the DB2 instance.

4. After successful installation, modify the `conf/application.properties` file to set the `db2.installation.directory` parameter to the installation path of the DB2 client.

5. The DB2 client version must strictly match the database version.

## Software Package Preparation

Please obtain the software package from our technical support based on actual needs: (xx.xx is the version number)

|Software Package |Description |
| --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| yashan-migrate-platform-xx.xx-linux-aarch64.zip <br/>yashan-migrate-platform-xx.xx-linux-x86-64.zip | YMP installation package for installation and deployment on Linux x86_64/arm64 platforms.                         |
| yashandb-xx.xx-linux-x86_64.tar.gz<br/>yashandb-xx.xx-linux-aarch64.tar.gz                          | YashanDB installation package for installing and deploying the default built-in database on Linux x86_64/arm64 platforms, requiring the same version as the migration target database. |
| instantclient-basic-linux.arm64-19.10.0.0.0dbru-2.zip<br/>instantclient-basic-linux.x64-19.19.0.0.0dbru.el9.zip | By default, the YMP migration tool has integrated JDBC drivers for corresponding versions of four database sources: Oracle, MySQL, DM, and YashanDB. During migration, the default method is to migrate via the JDBC interface. For data migration from Oracle to YashanDB, especially in scenarios involving GBK to GBK character set migration, it may be necessary for the entire migration path to avoid character set conversion. You can use the replace directive to add or replace OCI dependencies. The OCI version requirements are detailed in: [Supported OCI](#supportedDependenceVersion). |

All subsequent server installation steps starting from this step will be performed by the YMP installation user, so please switch to the YMP user or log in to the server as the YMP user.

The recommended installation path is `/home/ymp`, please ensure this directory is empty when installing YMP.

Upload the YashanDB installation package to the ymp user's `/home/ymp/` path:

```shell
# Operate as root user: Change the ownership of the installation package to ymp user
chown ymp:ymp yashandb-xx.xx-linux-x86_64.tar.gz

# Switch from root user to ymp user
su - ymp

# Change to the installation path
cd /home/ymp
```

<span id="supportedDependenceVersion" name="supportedDependenceVersion" class="yaslink"></span>

### Third-Party Dependency Versions

|Dependency |Version Requirement                                        |Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|:-------------------|:--------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JDK                | JDK17                                                               | 1. YMP only supports installation and usage in a JDK 17 environment (the built-in version is recommended).                                                                                                                                                                                                                                                                                                                                                                                                                          |
| OCI Client         | Version 19.19.0.0.0 or above                                        | The OCI environment must be prepared by downloading the OCI client from the [Oracle website](https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html#license-lightbox) and installing according to the steps listed on the official website.<br> Depending on the processor architecture, the recommended download and installation version information is as follows:<br>1. `instantclient-basic-linux.x64-19.19.0.0.0dbru.el9.zip` <br>2. `instantclient-basic-linux.arm64-19.10.0.0.0dbru-2.zip` |
| yasldr Tool        | Must be consistent with the version of the target YashanDB database |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

> **Note**:
> 
> If you need to migrate data from the source side to YashanDB versions 23.2 or 23.3, the OPENSSL version in the YMP environment must be consistent with the target environment, with version requirements referenced in the YashanDB product documentation. For possible issues, refer to: [Common Migration Issues](../../FAQ/Migration Task.html#problem27)

## Client Browsers

YMP supports the Google Chrome, Microsoft Edge, and Firefox browsers, and it is recommended to use the current newer versions.

|Browser Support |Required Version |
|:-------------- |:-------- |
| Google Chrome  | Version 88 and above |
| Microsoft Edge | Version 88 and above |
| Firefox        | Version 78 and above |