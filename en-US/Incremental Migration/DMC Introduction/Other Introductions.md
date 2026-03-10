## Fault Handling

The DMC component provides means such as throwing error codes and logging to assist users in analyzing, handling, and tracing back when exceptions occur.

### Error Codes

DMC captures all exception information during runtime and throws it to the client (see the table below) so that users can perceive and handle it.

In case of errors like `internal error`, please retain log data and contact our technical support for further assistance.

|Error Type |Error Code |Description |Mitigation Strategy |
| ----------------------------------- | ------ | -------------------- | ------------------------------------------------------------ |
| YdsNotFoundConnectorException         | 0001       | Connector not found   | Exception triggered by inputting unsupported connector, please enter the connector name correctly.       |
| YdsClientCliException                 | 0002       | Client exception      | Exception occurred in the client, please check the client runtime environment.                         |
| YdsEncryptException                   | 0003       | Password service exception | Exception triggered by incorrect password, please enter the correct password.       |
| YdsClientNetException                 | 0004       | Client network exception | Exception triggered by client failing to connect to the server, please check if the network between client and server can communicate normally. |
| YdsSqlException                       | 0005       | SQL execution exception | Database connection or SQL execution exception, please check if related connectors are working properly.        |
| YdsNotSupportDataTypeException        | 0006       | Unsupported data type exception | Exception triggered by reading unsupported data type.                           |
| YdsModifyLogLevelException            | 0007       | Modify log level exception | Exception triggered by using an illegal log level when modifying log level.                         |
| YdsNoTasksFoundException              | 0008       | Task not found exception | Exception triggered by trying to operate a non-existent task, please check if the current task is created or has been deleted; this exception will not be triggered at present. |
| YdsTaskOperationFailedException       | 0009       | Task operation exception | Task operation failed, please check the reason for failure based on the exception information.             |
| YdsStageNotFoundException             | 0010       | Task stage not found exception | Exception triggered by trying to enter a non-existent task stage.                         |
| YdsTaskResumeFailedException          | 0011       | Task resume failed exception | Exception triggered during task recovery, please check the cause of failure according to the exception information, and refer to the 'resuming task' section of the document. |
| YdsDeserializeException               | 0012       | Sequence deserialization exception | Exception triggered during Jackson sequence deserialization, please modify according to the exception information.            |
| YdsConfigFileParserException          | 0013       | Configuration file parsing exception | Exception triggered during configuration file parsing, please check and modify the configuration file based on the exception information.   |
| YdsStartPointTooOldException          | 0014       | SCN too old exception | Exception triggered when the difference between the SCN of the earliest active transaction and the current SCN exceeds the threshold, please check and modify the configuration file based on the exception information. |
| YasIllegalArgumentException           | 0015       | Illegal argument value exception | Please verify the passed parameters according to the error information.                                 |
| YdsConnectorException                 | 0016       | Connector connectivity exception | Connector connectivity check failed.                                     |
| YdsServerShutdownException            | 0017       | DMC service shutdown exception | Exception triggered by DMC service shutdown failure.                                    |
| YdsWriteConverterException            | 0018       | Error generating insert converter | DMC internal data type mapping write error, possibly due to the source connector sending erroneous data. |
| YdsTaskSubmitException                | 0019       | Task submit exception | Task submission exception, please perform related operations based on the prompt information.                       |
| YdsConnectionPoolException            | 0020       | Connection pool exception | DMC built-in connection pool exception.                                            |
| YdsMonitorException                   | 0021       | Monitoring module exception | DMC monitoring module exception.                                             |
| YdsStartPointNotSupportRunException   | 0022       | Special start exception | Exception caused by YStream not supporting this start point, please restart the task.              |
| YdsWatchdogException                  | 0023       | Watchdog program exception | Watchdog script program exception, please refer to the watchdog script documentation for operations based on the error prompt.     |
| YdsExpException                       | 0024       | Expression parsing error | Please enter the correct expression based on the prompt.                                   |
| YdsManualDdlException                 | 0025       | Manual DDL exception | Please make modifications according to the error prompt.                                   |
| YdsNotSupportException                | 0101       | Unsupported feature exception | Exception triggered by using unsupported features.                               |
| YdsUnexpectedException                | 0102       | Unexpected exception   | An unexpected exception has occurred.                                           |
| YdsExecutorException                  | 0103       | Executor exception     | Exception occurred during task execution.                                         |
| YdsFunctionException                  | 0104       | Operator exception     | Exception occurred during operator execution.                                       |
| YdsNotFoundFileException              | 0105       | File not found exception | Exception triggered by not finding the specified file when loading the configuration file, please check if the file path is correct based on the error information. |
| YdsDuplicateArgumentException         | 0106       | Duplicate parameter exception | When executing a command in non-interactive mode, multiple same parameters were specified, please check the command and remove duplicate parameter items. |
| YdsTableSplitException                | 0107       | Table split exception   | Exception occurred during table splitting before executing full data sync, usually due to failure in obtaining the rowid of the table. |
| YdsExecutionException                 | 0108       | Async thread execution exception | Exception failure during async thread execution.                                       |
| YdsUnexpectedInterruptedException     | 0109       | Unexpected interruption exception | Unexpected interruption exception during execution.                                   |
| YdsSnapshotToleranceException         | 0110       | Full migration fault tolerance exception | Exception that occurs when executing full migration after enabling full migration fault tolerance; this is an internal exception provided to YMP during DMC component work. |
| YdsNotSupportVariantNameException     | 0111       | Illegal name exception   | Exception triggered by the source side defining unsupported naming during the incremental synchronization phase.           |
| YdsNotSupportCharsetException         | 0112       | Character set exception  | Exception thrown due to DMC not supporting the character set.                              |

### Runtime Logs

When a fault occurs, the runtime logs will record not only error codes and fault prompts, but also detailed JVM stack information at the time of the fault. Users can infer the cause of the fault through the log context.

In some scenarios, to obtain more information to facilitate locating the cause of the fault, the logging level can be adjusted (default is info), but more detailed logging may affect the performance of migration tasks, please use it cautiously.

### Common Faults

**How to handle task failure due to unexpected situations during incremental synchronization phase?**

Generally, the incremental synchronization phase is a long-running process. It is recommended to manually back up the configuration file on the server so that in case of unforeseen exceptions (such as related link server disconnection, power outage, etc.) causing incremental task synchronization failure, the task can be resumed using the resume functionality (use the `resume` command on the client for resuming).

The task recovery during DMC resume functionality depends on the source connector's capability to resume (such as YashanDB's YStream resume), and two auxiliary tables for resume on the target connector. If an exception occurs on the source connector (for example, if YashanDB's YStream service is stopped or deleted), or if the auxiliary tables for the resume on the target side are unexpectedly lost or damaged, the task cannot continue through the resume functionality.

**What to do if synchronization fails due to insufficient memory caused by unreasonable memory planning, resulting in server not responding?**

If the server operation logs contain `java.lang.OutOfMemoryError: Java heap space` due to not properly planning memory for the DMC component during the installation and deployment phase, you can take the following actions to troubleshoot:

- If the task is in the metadata synchronization or full data synchronization phase and cannot be resumed; you need to stop the current task and DMC server, check the environment and expand resources, and then restart the server to initiate a new migration task.

- If the task is in the incremental synchronization phase, you can use resume to restore it, i.e., stop the current task and DMC server, check the environment and expand resources, then restart the server, and use the `resume` command for resuming. Alternatively, you can adopt the same approach to start a new migration task.

- In some cases, insufficient memory may cause the client to fail to connect to the server to perform stopping operations, which is usually due to the network listening thread of the server's Tomcat container being shut down by the JVM due to a heap overflow error. At this time, you can use the jps command or other methods to check the PID of the living Java process (use Task Manager to view Java processes on Windows) and terminate it.

```shell
$ jps
17698 run-boot.jar
$ kill -9 17698
```

> **Note**:
>
> When there is insufficient memory resources, it is strongly recommended to check the environment to ensure sufficient resources before restarting the DMC service to ensure that the JVM can operate normally, rather than continuing to run tasks with the existing server.

**Timeout when executing startup/shutdown/resume/remove/run commands**

The DMC client and server communicate through the operating system's local interface or the network. A timeout of 10 to 30 seconds is set for the startup/shutdown/resume/remove commands. If there is no response after the time limit, it may be due to the server executing the command too slowly.

The server executing the command slowly may be caused by corresponding connectors being stuck due to various reasons. You may try to check the session status of the connector and close unreasonable sessions to retry the task. Below are some common methods to query the session status of connectors:

::: tabs

== YashanDB

```sql
SELECT
    S.SID,
    S.SERIAL#,
    S.USERNAME,
    S.CLI_PROGRAM,
    S.STATUS,
    S.LOGON_TIME,
    Q.SQL_TEXT
FROM
    V$SESSION S
        JOIN
    V$SQL Q
        ON
    S.SQL_ID = Q.SQL_ID
WHERE
    S.USERNAME IS NOT NULL;
```

== Oracle

```sql
SELECT
    S.SID,
    S.SERIAL#,
    S.USERNAME,
    S.PROGRAM,
    S.STATUS,
    S.OSUSER,
    S.LOGON_TIME,
    Q.SQL_TEXT
FROM
    V$SESSION S
        JOIN
    V$SQL Q 
        ON S.SQL_ADDRESS = Q.ADDRESS
WHERE
    S.USERNAME IS NOT NULL;
```

== MySQL

```sql
SHOW FULL PROCESSLIST;
```

:::

The run command requires additional attention to the task client parameter `executeRunTimeout`. When this parameter is too small, it may easily result in `Run task timeout` errors. After encountering this error, you can use `show status --detail` to check if the task was submitted successfully and to view the task status.

If the startup command times out, after starting, you can try to use the ping command to check if the startup was successful. Failure to start may be due to unreasonable configuration parameters, such as the port being occupied by another application, JVM startup parameters being incorrect, or memory allocation being unreasonable.

If the shutdown command times out, you may try to terminate the service process using the operating system's local interface.

**When starting a task with YAC as the source end, YAS-01202 error is reported.**

When starting a task, DMC needs to query the view GV_$TRANSACTION. If there is communication failure among nodes in the cluster, the task will fail. Please check the status of each node, and restart the task afterward.

**The lag metric shows high latency when using show metrics.**

The lag metric reflects the difference in time between when the DMC server receives the database change event and when the source database actually executes the change event.

If there is a latency issue, please check if the clocks of the source database and the server hosting DMC are synchronized. In a Linux system, you can execute the `date -R` command in the shell to check the server time.

If the server clock is not accurate, it needs to be calibrated. You can use NTP for clock calibration.

**Lock failure when synchronizing MySQL.**

Lock failure is usually due to a large number of active transactions on the source side causing lock contention or lock occupation. You can consider the following three solutions:

1. Choose to start the task when there is less business on the source side.
2. Increase the waiting timeout in the task configuration file `source.options.connectorParameter.readLock.lockTimeout`, and concurrently adjust the client parameter `executeRunTimeout` to avoid client timeout.
3. Resubmit the task to retry obtaining the lock.

**YDS-104 error reported during incremental watermark push task execution for Oracle Sequence.**

To push the HWM of the Oracle sequence, you need to enable supplemental logging for sys.seq$. Execute the following statement:

```sql
ALTER TABLE sys.seq$ ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS
```

**For Oracle database on the target end, metadata/incremental data synchronization reports ORA-00910 error.**

The ORA-00910 error indicates that the specified length exceeds the maximum limit for that data type when defining columns. Please refer to the relevant Oracle documentation for details.

The common reasons for this error in DMC are:

1. VARCHAR or VARCHAR2 column types exceed 4000 characters (or exceed 32767 characters when set to extended mode in Oracle).
2. CHAR column types exceed 2000 characters.
3. Other data types like NUMBER, DATE, etc., may also have similar restrictions in formatting or other contexts.

Please check the data types to confirm that the defined column data types and lengths comply with the restrictions. If longer VARCHAR or VARCHAR2 data needs to be stored, ensure that the target database is set to EXTENDED mode.

**Error "Metadata validation failed" reported when resuming a task.**

For failures occurring right during DDL execution, the resume operation cannot guarantee exactly once. You can roll back the last DDL on the target before resuming.

**For YashanDB/Oracle, full synchronization reports YAS-02020/ORA-01555 error.**

Increase the value of `UNDO_RETENTION` parameter on the source database, or use a larger undo tablespace.

**Temporary tablespace insufficient on the target side during full synchronization of large LOB table data causing task failure.**

Try to enlarge the temporary tablespace size on the target side, reduce the concurrency of the DMC task's `options.snapshotParallelism`, and configure reasonable `sink.options.lobSizeThreshold` to ensure that there is no lack of temporary tablespace on the target side during synchronization.

**When synchronizing from MySQL to YashanDB, an empty string for the primary key on the source end reports YAS-04006 error upon insertion into YashanDB.**

By default, YashanDB treats empty strings as NULL values. Please execute `select VALUE from V$PARAMETER where name = EMPTY_STRING_AS_NULL` to check if the structure is TRUE. Ensure this value is set to FALSE; otherwise, an empty string inserted into YashanDB will be treated as a NULL value, violating the non-null constraint.

**YAS-02014 error reported while synchronizing data to YashanDB.**

This error indicates that the row length exceeds the YashanDB specifications. Please adjust the source table structure to split the columns into different tables for synchronization.

**When the source is Oracle, full synchronization reports ORA-00942 error.**

Typically, this indicates that the connecting user does not have the privileges on the respective table. Please check the privilege configuration.

**YAS-00103 error reported while synchronizing data to YashanDB.**

This may be due to unreasonable parameters configured on the target side, leading to insufficient resources. Please check and adjust YashanDB's `WORK_AREA_POOL_SIZE` parameter.

**When the source is YashanDB, and the incremental task fails to start with an error message: YAS-02020 snapshot too old.**

When this exception occurs, please commit or roll back long-running transactions on the source side, and then try to restart the incremental task.

**Error "the target object already exists" reported during resume recovery failure.**

For failures occurring right during DDL execution, the resume operation cannot guarantee exactly once. You can use the `--ignore-first-ddl` parameter to resume, ignoring the first executed DDL.

**When the source is YashanDB and there are large transactions, task synchronization delay increases, and resume recovery is slow.**

When executing the migration task, the presence of large transactions on the source side should be avoided as much as possible. If it is indeed necessary and a failure occurs during a large transaction synchronization, and the resume recovery is slow, you can refer to the official documentation regarding YStream for YashanDB for adjustments to YStream-related parameters, or configure better parameters during the task startup to reduce such problems.

**If on YashanDB the user is dropped by mistake during the incremental phase, all tables under that USER on the target end are deleted.**

If such actions are performed mistakenly on the source side, YashanDB will first cascade delete all objects under that USER, and YStream will also capture these LCRs. At this point, DMC will not regard this as an exception, but will parse it into a DROP TABLE operation to apply on the target side. However, the DROP USER operation will not be applied, thus the target table's user will still exist but all its under-tables will be deleted.

**Negative synchronization delay shown during incremental data synchronization.**

In the case of DML, when DMC synchronizes this DML, the synchronization delay is the current time on the server where DMC is located minus the disk write time of that DML in the source database. If the server where the source database is located has time ahead of that of the DMC server, the synchronization delay will be negative.

Please check if the times of the source database and the server where DMC is located are accurate. If not accurate, adjust the server clocks to ensure synchronization of the clocks between the source database and the DMC server.

**Error "In 'INITIAL' mode, parameter 'sink->dataSource->properties->rewriteBatchedStatements' and parameter 'sink->options->checkDml' cannot both be true at the same time" reported during incremental synchronization to MySQL.**

Due to limitations in MySQL JDBC, the values of `sink.dataSource.properties.rewriteBatchedStatements` and `sink.options.checkDml` cannot both be true at the same time. Please set at least one of them to false when configuring the task.

**When incrementally synchronizing from MySQL to YashanDB and the character set range of the source exceeds that of the target.**

DMC does not perform character set conversion and synchronization. Please ensure that the target's character set supports the range compatible with the source's character set, or list the columns of related tables exceeding the range as a blacklist to ignore synchronization.

**When incrementally synchronizing from MySQL to YashanDB and the schema character set on the source has changed.**

DMC does not support incremental DDL synchronization at the schema level. Please ensure not to modify the character set of the schema currently being synchronized during the incremental phase, otherwise, there will be risks of data corruption for newly added table data during synchronization.

**Incremental synchronization failure with error message: YDS-0102 Unexpected exception: YDS-0005 Sql execute error [SINK]: Batch xxx execute failed.**

This error indicates that the number of rows updated on the target does not match the source result during DML synchronization; this is often caused by pre-existing data on the source when starting the incremental task. You can set the synchronization mode in the task configuration file (`options->syncMode`) to `initial` and restart the task to resolve this.

**When the source is Oracle, incremental synchronization reports an error "containing special characters cannot be incrementally synchronized."**

When the schema or table name in Oracle contains special characters like `{}\()[].``~\`, LogMiner cannot parse, and DMC correspondingly intercepts reporting an error.

**During data synchronization, if there is a network fault on the target server without the synchronization tool's awareness, the task continues to run normally without errors.**

The access to the target database relies on JDBC. Due to network faults, JDBC connections performing operations may block pending until the network is restored, and the synchronization tool may not be aware of this and may not report errors. If this occurs, check the logs or reach out for support from relevant technical personnel.

When executing `show flashback`, the flashback points cannot be obtained, and the message" The flashback points at both ends have not been retrieved yet", you may execute again later is displayed. The `show flashback` command can retrieve flashback points normally.

When using the SHOW FLASHBACK function, if a large transaction is encountered during breakpoint resume transmission, FLASHBACK will not refresh the data until the entire large transaction is fully received.

Renaming a table in the blacklist during incremental synchronization triggers the error: "YDS-0102 Unexpected exception: YDS-0101 Not support exception: Tables in the blacklist do not support the "rename table" operation. Please add the new table name to the blacklist."

During incremental synchronization, renaming tables in the blacklist is prohibited. You need to manually add the new table name to the blacklist to skip the table and resume incremental synchronization.

<span id="jexl" name="jexl" class="yaslink"></span>

## JEXL Syntax

When performing custom mapping of data types, users need to use the Java Expression Language (JEXL) scripting language. Below is the main syntax of this language, for more detailed explanations please refer to [Apache commons Jexl 3.5 Official Documentation](https://commons.apache.org/proper/commons-jexl/reference/syntax.html).

### Language Elements

|Category |Syntax/Rules |Example |
| :----------------------- | :----------------------------------------------------------- | :----------------------------------- |
| **Comment**                | - Line comments: `##` or `//`                                 | `## End of line comment`           |
| **Identifier / Variable**  | - Begin with a letter, `_`, or `$`, can be followed by letters, digits, `_`, or `$` | Valid: `var1` `_a99` `$x` `foo.bar` |
| **Script Structure**       | Zero or more statements; can contain `pragma`; defaults to returning the value of the last statement; can use `return` for explicit return | `let a = 1; a + 2` ⇒ 3              |
| **pragma** (compile-time directive)| Format: `#pragma key value`                                     | Effective only at compile time, cannot be changed at runtime |
| **Statements**             | Empty statement `;`, block `{...}`, variable declaration, expression, condition, loop | `;` `{ let x = 1; }`                |
| **Block**                  | `{ stmt1; stmt2; }`                                          |                                    |
| **Local Variable**         | - `let`: block scope, cannot be redeclared                    | `let x = 1; const y = 2; var z = 3;` |
| **Expression**             | Literal, variable, assignment, access, function/method calls, operations |                                    |
| **Assignment**             | `bean.prop = val` or `ant['prop'] = val`                     | `user.name = "JEXL"`               |
| ** Function Definition**     | Various forms:                                                 | `var t = 20; var add = x -> x + t;` |
| **Function / Method Call**     | `f(arg1, arg2)` or `"hello".length()`                          |                                    |
| **Access Operator**        | - `.` or `[]`                                                | `user?.profile?.name`              |
| **@ Annotation**           | Meta-statement, wrap execution: `@synchronized(x) x.inc()`    | Can be chained: `@lenient @silent expr`      |

### Literals

|Type |Syntax |Example |
| :------------- | :-------------------------------------- | :--------------------------------- |
| **Integer**    | Decimal, Octal `0`, Hexadecimal `0x` | `42` `010` `0x2A`        |
| **Float**      | Decimal + `f/F/d/D` or scientific notation | `3.14` `2e-3d`           |
| **High Precision** | BigInteger: `42h` / BigDecimal: `42.0b` |                           |
| **NaN**        | Directly use `NaN`                      |                           |
| **String**     | Single/Double quotes; escape `\`; Unicode `\u0061` | `"Hello"` `'JEXL'` `"line\nbreak"` |
| **Template String** | Backticks, interpolable              | \`Hello ${user}\`        |
| **Regex**      | `~/pattern/`                          | `~/^[A-Z]+$/`            |
| **Boolean**    | `true` / `false`                      |                           |
| **null**       | `null`                                  |                                    |
| **Array**      | `[elem1, elem2]` → Strongly typed array | `[1, 2, 3]` → `int[]`   |
| **List**       | `[elem,...]` → `ArrayList`              | `[1, 2,...]`                       |
| **Set**        | `{elem1, elem2}` → `HashSet`            | `{"a", 1}`                         |
| **Map**        | `{k1:v1, k2:v2}` → `HashMap`            | `{"name":"JEXL"}`                  |
| **Range**      | `start..end` → Iterable                 | `for (i : 1..5)`         |

### Built-in Functions

|Function |Description |Example |
| :---------------------- | :---------------------------------- | :---------------------- |
| `empty(x)`                 | Checks for emptiness: null, empty string, empty collection/array, etc. | `empty("")` ⇒ true    |
| `size(x)`                  | Length/size; supports custom `size()` method | `size("abc")` ⇒ 3     |
| `new(className, args…)`    | Reflection to create objects          | `new("java.util.Date")` |
| **Top-level Function**      | Registers to `JexlContext`/`JexlEngine` | `string(42)`           |
| **Namespace Function**      | `ns:method(args)`                  | `math:cos(0)`         |

### Operators

#### Logical / Bitwise Operations

|Symbol |Description |Overload |
| :-------------------------- | :----------------- | :----- |
| `and / &&`                     | Logical AND      | ×        |
| `or / |`                       | Logical OR       | ×        |
| `not / !`                      | Logical NOT      | ×        |
| `&` `^` `~`                    | Bitwise AND, OR, XOR, NOT | √ |
| `<<` `>>` `>>>`                | Bitwise Shift    | √        |

#### Comparison / Pattern Matching

|Symbol |Description |Example |
| :------------ | :------------------------- | :--------------------- |
| `==` / `!=`     | Loose equality (calls `equals`) | `"42" == 42`           |
| `===` / `!==`   | Strict equality (type different means different) | `"42" !== 42`          |
| `< <= > >=`     | Comparison                      | Supports aliases: `lt le gt ge` |
| `=~` / `!~`     | Regex match / not match         | `"abc" =~ ~/a.*/`      |
| `=^` / `!^`     | startsWith shortcut              | `"abc" =^ "a"`         |
| `=$` / `!$`     | endsWith shortcut                | `"abc" =$ "c"`         |

#### Arithmetic / Others

|Symbol |Description |Overload |
| :--------------------------- | :--------- | :----- |
| `+ - * / div`               | Addition, subtraction, multiplication, division | √ |
| `% mod`                     | Modulus       | √         |
| `+= -= *= …`                | Side-effect assignment | √     |
| `+` `-`                     | Unary plus/minus | √     |
| `empty` / `size`            | Unary operators | √     |
| `instanceof` / `!instanceof` | Type check    |         |

### Access Patterns

|Scenario |Syntax |Note |
| :-------------- | :-------------------------- | :-------------------- |
| **Array / List**  | `arr[0]` or `arr.0`       |                      |
| **Map**           | `map[key]` or `map.'key'` | `map.7` ≠ `map['07']` |
| **JavaBean**      | `obj.prop` or `obj['prop']` | Calls `getProp()`   |
| **Indexed Property** | `obj.attr['idx']`       | Calls `getAttr(String)` |
| **Public Field**  | `obj.field`               |                      |
| **Duck-Typing**   | `obj.dynamic`             | Calls `get(String)`  |

### Conditional Statements

|Statement |Syntax |Example |
| :------------------- | :---------------------------------- | :---------------------------- |
| **if / else**        | `if (cond) { ... } else { ... }`    |                               |
| **for**              | 1. `for (let v : iterable) { ... }` | `for (let i : 1..3) sum += i` |
| **while / do-while** | `while (cond) { ... }`              |                               |

### Control Flow

|Statement |Description |Example |
| :--------------------- | :--------------- | :------------------------------------------------ |
| **continue**               | Skip current loop      | `for (i : 1..5) { if (i==3) continue; }`      |
| **break**                  | Exit loop              | `while(true) { break; }`                       |
| **return**                 | Return value and terminate script | `function f(x) { return x * 2; }` |
| **throw**                  | Throw exception        | `throw new("java.lang.IllegalStateException")` |
| **try/catch/finally**      | Exception handling       | `try { 1/0 } catch(e) { log(e) } finally { ... }` |
| **try-with-resources**    | Automatic resource closure | `try (let r = open()) { r.read() }`           |

### Reserved Keywords

As follows:

or and eq ne lt gt le ge div mod not null true false new var do while break continue function return

If the property name conflicts with a keyword, use `obj.'new'` or `obj['new']`.