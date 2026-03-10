This document describes the commands for the YMP startup script.

## Command Description
|Command |Description |
|----------------|---------------------------------|
| install          | YMP installation and deployment command, used for built-in library deployment |
| start            | YMP startup command, used for built-in library deployment |
| stop             | YMP stop command, used for built-in library deployment |
| restart          | YMP restart command                            |
| uninstall        | YMP uninstall command                          |
| deploy           | YMP command to start the visual deployment of the db2 client |
| installnodb      | YMP installation command, used for external library deployment |
| startnodb        | YMP startup command, used for external library deployment |
| stopnodb         | YMP stop command, used for external library deployment |
| restartnodb      | YMP restart command, used for external library deployment |
| password         | YMP change password command                    |
| connection       | YMP change connection information command      |
| upgrade          | YMP upgrade command                            |
| replace          | YMP tool replacement command                   |
| useLocalJdk | YMP invokes JDK commands from the locally specified path, and the commands are not interoperable when in use. |
| status           | YMP status inquiry command                     |
| -h/--help        | YMP help inquiry command                       |
| -v/--version     | YMP version inquiry command                    |

## install

|Parameter |Description |
|---------|----------------------------|
| --db       | Required parameter, specifies the built-in library installation package, and is also used for the installation of yasldr and exp tools |
| --path     | Optional parameter, specifies the OCI path   |

Example:
````shell
$ sh bin/ymp.sh install --db /data/db/yashandb-23.2.4.100-linux-x86_64.tar.gz --path /data/oci/instantclient_19_19/ --deps /data/deps/yashandb-deps-23.2.4.100-linux-x86_64.tar.gz
````

## start

Example:
````shell
$ sh bin/ymp.sh start
````

## stop

Example:
````shell
$ sh bin/ymp.sh stop
````

## restart

Example:
````shell
$ sh bin/ymp.sh restart
````

## uninstall

|Parameter |Description |
|----|------------------------------------|
| -f        | Optional parameter, force uninstall when specified |

Example:
````shell
# Switch to YMP installation directory
$ sh bin/ymp.sh uninstall -f
````

<span id="db2_deploy" name="db2_deploy" class="yaslink"></span>

## deploy

|Parameter |Description |
|--------|------------------|
| --path    | Required parameter, specifies the db2 client installation package |

Example:
````shell
# Switch to YMP installation directory
$ sh bin/ymp.sh deploy --path v10.5fp11_linuxx64_client.tar.gz
````

## installnodb

|Parameter |Description |
|---------|----------------------------|
| --db       | Required parameter, specifies the built-in library installation package, and is also used for the installation of yasldr and exp tools |

Example:
````shell
$ sh bin/ymp.sh installnodb --db /data/db/yashandb-23.2.4.100-linux-x86_64.tar.gz
````

## startnodb

Example:
````shell
$ sh bin/ymp.sh startnodb
````

## stopnodb

Example:
````shell
$ sh bin/ymp.sh stopnodb
````

## restartnodb

Example:
````shell
$ sh bin/ymp.sh restartnodb
````
<span id="password" name="password" class="yaslink"></span>

## password

|Parameter |Description                                                                                                         |
|------------------|----------------------------------------------------------------------------------------------------------------------------|
| --sys            | Optional parameter, change the sys password specified during built-in library deployment (YASDB_PASSWORD in db.properties) |
| --user           | Optional parameter, change the connection user password (spring.datasource.password in application.properties)             |
| --reset          | Optional parameter, reset the admin user login password to admin                                                           |

Example:
````shell
$ sh bin/ymp.sh password --sys Ymppw602.
$ sh bin/ymp.sh password --user Ymppw602.
$ sh bin/ymp.sh password --reset
````

## connection

|Parameter |Description |
|---------|----------------------------|
| --url      | Optional parameter, modify the business database connection |
| --username  | Optional parameter, modify the business database connection user |
| --password   | Modify the business database connection user password |

Example:
````shell
$ sh bin/ymp.sh connection --url 127.0.0.1:1688 --username ymp --password Ymppw602.
````

## upgrade

|Parameter |Description |
|---------|----------------------------|
| --from     | Required parameter, specifies the source YMP path for upgrade |
| --db       | Optional parameter, functions the same as during deployment |
| --path     | Optional parameter, functions the same as during deployment |

Example:
````shell
$ sh bin/ymp.sh upgrade --from /old/yashan-migrate-platform --db /db/yashandb-23.2.4.100-linux-x86_64.tar.gz --path /oci/instantclient_19_19/ --deps /deps/yashandb-deps-23.2.4.100-linux-x86_64.tar.gz
````

## replace

|Parameter |Description |
|---------|----------------------------|
| --yasldr   | Optional parameter, specify a new database installation package to replace yasldr |
| --exp      | Optional parameter, specify a new database installation package to replace exp |

Example:
````shell
$ sh bin/ymp.sh replace --yasldr /db/yashandb-23.2.5.100-linux-x86_64.tar.gz
$ sh bin/ymp.sh replace --exp /db/yashandb-23.2.5.100-linux-x86_64.tar.gz
````

## useLocalJdk

Example:

````shell
$ sh bin/ymp.sh install --db /db/yashandb-23.2.4.100-linux-x86_64.tar.gz --useLocalJdk
$ sh bin/ymp.sh installnodb --db /db/yashandb-23.2.4.100-linux-x86_64.tar.gz --useLocalJdk

$ sh bin/ymp.sh start --useLocalJdk
$ sh bin/ymp.sh startnodb  --useLocalJdk


$ sh bin/ymp.sh restart --useLocalJdk
$ sh bin/ymp.sh restartnodb  --useLocalJdk
````

## status

Example:
````shell
$ sh bin/ymp.sh status
````

## -h/--help

Example:
````shell
$ sh bin/ymp.sh -h
$ sh bin/ymp.sh --help
````

## -v/--version

Example:
````shell
$ sh bin/ymp.sh -v
$ sh bin/ymp.sh --version
````
