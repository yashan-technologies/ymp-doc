The DMC server can be deployed on the same server as the source or target, or on a separate server, but it must meet the environmental requirements and preparations listed in this document.

## Environmental Requirements

The environmental requirements include two parts: the requirements for server resources and the requirements for the application environment.

### Server

|Item |Configuration Requirements |Minimum Configuration |
| -------- | ----------------------------------------------------------- | --------- |
| Operating System | CentOS, KylinOS V10 and other Linux systems<br>Windows (not recommended) | -                     |
| CPU          | X86_64, ARM64, recommended 4 cores or more                                      | CPU cores > 1         |
| Memory       | Recommended 16G or more                                                          | >5G                   |
| Hard Disk    | Recommended storage space 50G or more                                            | >1G                   |
| File System  | No special requirements                                                           | -                     |
| Network      | Gigabit Ethernet or higher, supports TCP                                          | -                     |

> **Note**:
>
> - The memory should be greater than the JVM parameters -Xms and -Xmx (default is 4G) to ensure that the JVM starts normally.
> - The memory that Java applications have already allocated might also be assigned by the operating system to other applications. Therefore, when DMC shares a server with other applications, memory resources should be scaled up according to application requirements.
> - The network connectivity, latency, and throughput in the synchronization link will significantly affect synchronization performance, and must be ensured to be smooth.

### Application Environment

|Item |Configuration Requirements |Minimum Configuration |
| ---- | ------------ | -------- |
| Java      | Java 17 and above          | Java 17               |

> **Note**：
>
> It is recommended to keep the -Xms and -Xmx configurations (default is 4G) consistent in production environments. The detailed meanings of the parameters can be found at [Java HotSpot VM Options](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html).

## Preparations Before Deployment

Please complete the preparations for the DMC server deployment as listed below.

### Server Clock Calibration

The DMC server clock must be consistent with the clock of the source server. Execute the following command in Linux to check the clock:

```shell
$ date -R
```

If the DMC server clock is inconsistent with the source server clock, please contact the network administrator to calibrate the DMC server clock.

### Create User

Create an installation user for the DMC component and switch to that user (example for Linux):

```shell
# useradd -d /home/dmc -m dmc
# passwd dmc
# su - dmc
```

### Create Software Directories

Create DMC software directory and JDK software directory separately (example for Linux, please modify to the actual path):

```shell
$ cd ~
$ mkdir dmc
$ mkdir jdk
```

### Install JDK

Taking Oracle JDK as an example (please modify to the actual path and name):

::: tabs

== Linux

1. Go to [Oracle official website](https://www.oracle.com/java/technologies/downloads/), select the JDK 17 or above installation package and download it to the JDK software directory, then unzip:

```shell
$ cd ~/jdk
$ tar xvf jdk-24_linux-x64_bin.tar.gz
```

2. Configure environment variables:

```shell
$ echo -e '\n# set java eviroment\nexport JAVA_HOME=/home/dmc/jdk/jdk-24.0.2\nexport PATH=$JAVA_HOME/bin:$PATH\n' >> ~/.bashrc
$ source ~/.bashrc
```

3. Check the Java version; successful output indicates that the JDK installation was successful:

```shell
$ java -version
java version "24.0.2" 2025-07-15
Java(TM) SE Runtime Environment (build 24.0.2+12-54)
Java HotSpot(TM) 64-Bit Server VM (build 24.0.2+12-54, mixed mode, sharing)
```

== Windows

1. Go to [Oracle official website](https://www.oracle.com/java/technologies/downloads/), select the JDK 17 or above installation package (exe or msi file) and download it to the JDK software directory.
2. Run the JDK installation file, and choose default or custom options as needed during the installation process.
3. Open the cmd window and check the Java version; successful output indicates that the JDK installation was successful:

```shell
> java -version
java version "17.0.16" 2025-07-15 LTS
Java(TM) SE Runtime Environment (build 17.0.16+12-LTS-247)
Java HotSpot(TM) 64-Bit Server VM (build 17.0.16+12-LTS-247, mixed mode, sharing)
```

:::

## Deploy DMC

Please follow the steps below to complete the deployment of the DMC server (please modify to the actual path and name):

1. Contact our technical support to obtain the DMC component package and place it in the DMC software directory.
2. Unzip (example for Linux):

```shell
$ cd ~/dmc
$ tar xvf yas-data-sync-23.4.7.2.tar.gz
```

3. Enter the DMC installation directory; seeing the following content indicates that the deployment was successful:

```shell
$ cd yas-data-sync
$ ls
bin  conf  lib  LICENSE  Open Source Software Statement  script
```

4. Edit the server startup configuration file ({DMC installation directory}/conf/yds_config.yml), modify the configuration items as needed. For the [Security Configuration](Security Configuration) and [Monitoring Configuration](Monitoring Configuration) items, please refer to the corresponding documentation. For other configuration items, please refer to the [Server Startup Configuration File Introduction](../DMC Introduction/Configuration Files):

```shell
$ vi ./conf/yds_config.yml
```
