The security configuration of the DMC component includes two parts: key configuration and audit configuration.

## Key Configuration

When DMC runs migration tasks, the passwords required to connect to the source and target are stored in the configuration file in encrypted form (generated using the [encryptpwd command](../DMC Introduction/User Interfaces.html#encryptpwd)).

DMC uses the AES encryption algorithm to ensure password security, supporting three key lengths: 128-bit, 192-bit, and 256-bit, which correspond to AES-128, AES-192, and AES-256 respectively. The longer the key length, the higher the security, but the performance of encryption and decryption may slightly decline.

When deploying the DMC server, the system includes a default key (located at {DMC installation directory}/conf/passwd/secret.key) with a length of 128 bits.

```shell
$ cd ~/dmc/yas-data-sync
$ cd conf/passwd
$ ll
-rw-rw-r-- 1 dmc dmc 16 Aug 14 10:25 secret.key
```

For security reasons, it is recommended that users replace this key by entering the key directory. Below is an example command to generate a new key:

```shell
$ openssl rand -out secret.key 16
```

> **Note**:
>
> When starting the DMC service, the key file will be set to be readable only by the DMC installation user (400 privilege). After that, if a key replacement is required, the DMC service must be stopped, write privilege added to the key file, and then the new key generation executed.

## Audit Configuration

DMC provides audit log functionality ({DMC installation directory}/logs/{DMC instance name}/{migration task name}{timestamp}/auditLog.log) to record information such as SQL statements executed during the migration task and the data read and inserted, making it easier for users to backtrack afterward.

Users can configure the following audit strategies according to actual needs:

- off mode: auditing is not enabled
- ddl mode: records SQL statements executed on the source and target during migration tasks, including SQL text and execution results
- all mode: in addition to recording SQL statements executed on the source and target, it also records data read from the source and data inserted into the target.

**Example**

```shell
# ddl mode audit log
2024-07-25 16:44:58.951 [http-nio-127.0.0.1-8100-exec-8]  sink DDL - CREATE TABLE "$YDS_CONF_YDS_INSTANCE" ("TASK_NAME" VARCHAR(480 byte), "CONFIG" CLOB, "SOURCE_TYPE" VARCHAR(120 byte), "METADATA" CLOB, PRIMARY KEY ("TASK_NAME")), Execute result: FAILED

2024-07-25 16:45:04.616 [yds-snapshot-source-thread-1]  source DDL - SELECT "COL0" FROM "SCHEMA"."TABLE" AS OF SCN 590068936052350976, Execute result: SUCCESS
```

The default configuration for the audit log on the DMC server is off, meaning auditing is not enabled. It also supports modification via the following methods:

**Configuration File**

In the server startup configuration file ({DMC installation directory}/conf/yds_config.yml), modify the corresponding items; after starting the DMC service, it will execute the corresponding audit strategy based on this value.

```yml
logging:                                              # Log level
  level: info                                         # Run log level
  audit: all                                       # Audit log level
  auditLogAllEffectTime: 10                       # Default effective time for command line to enable all mode
```

**CLI Command**

DMC supports instant adjustment of the audit strategy during the migration task run, but there are effective time controls for all mode. For example:

```shell
# According to the configuration file, all mode is effective for default 10 minutes
YDS@8100> set parameter auditLogLevel all
# Specify all mode effective for 30 minutes
YDS@8100> set parameter auditLogLevel all --effect-time 30
```

> **Note**:
>
> - The all mode audit will record all data flowing through DMC, requiring higher resources, and should be used with caution.
>
> - At the three points in time of starting the DMC service, enabling all mode by CLI command, and the expiration of the effective time for all mode, DMC will set all auditLog.log files' privilege to be readable and writable only by the current operating system user (600 privilege).