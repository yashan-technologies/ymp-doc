##### About DmlWriteCount Statistics

For tables containing LOB types (BLOB, CLOB, NCLOB, VARCHAR2, NVARCHAR2, etc.), the INSERT and UPDATE operations will be split into several DMLs, thus the DmlWriteCount statistics will be greater than the actual DMLs occurring in the source database.

##### Incremental Phase Error 'OStream-0101 UnsupportedOperation: Unsupported event, event type: DDL row: LogMinerEventRow'

In the real-time data synchronization scenario with Oracle, filtering incremental DDLs is a key challenge to ensure data consistency and high availability of the synchronization task:

1. Business Pain Points:
   - The Oracle database automatically performs related DDL operations periodically, such as compressing and reclaiming space (alter table xxx shrink space xxx).
   - Unnecessary DDL operations can lead to disruptions in the synchronization link or inconsistencies between the target structure and the source structure.
   
2. Technical Bottlenecks:
   - Currently,  does not have the ability to parse DDLs and will throw errors when unsupported DDLs are encountered.
   - It cannot identify whether the DDL was triggered automatically by the Oracle database or manually by the user.

Solution:

Users configure regular expressions, and Oracle stream builds a regex matching engine based on these expressions to filter and warn about incremental DDLs accordingly.

Workflow:

1. DMC loads regex configuration.
2. Oracle stream builds a regex matching engine based on the regex.
3. Oracle stream captures incremental DDLs for matching through the regex matching engine.
4. If matched successfully, it filters and warns while logging the event.
5. If matching fails, it synchronizes downstream.

Core Components:

- Incremental Capture Layer: Oracle stream parses incremental events.
- Regex Engine: User regex rules converted into a regex matching engine.
- Regex Execution Layer: Incremental DDLs are matched by the regex engine. Successful matches are filtered and warned, logging the event.

Scenario 1: Filter out Oracle's automatic compression and space reclamation (e.g., ALTER TABLE xx SHRINK SPACE).

- Problem: Automatically triggered incremental DDLs in Oracle may pose a risk of disrupting the synchronization link.

- Step 1: Configure task configuration file task_config.yml

```yaml
source: # sink connector option
  type: Oracle # sink connector type
  dataSource:
    hostname:
      - host:  # Oracle monitor ip or hostname
        port:  # Oracle monitor port
    linkType: serviceName # sid or serviceName
    database:  # sid or serviceName value
    pdbName: 
    username:  # link userName
    password: 
  options:
      connectorParameter:
        _filterDdlPattern:
            - ^(?i)ALTER\s+TABLE\s+.*SHRINK\s+SPACE.*
```

- Step 2: Start the task and verify

```shell
# YDS Client
YDS@8100> run

# Oracle Database
# Assuming the task is configured to synchronize tables under TESTFF1 user
ALTER TABLE TESTFF1.TABLE_CHAR SHRINK SPACE;

# Check task logs (run.log) output example:
2025-07-02 15:25:57.835 [oracle-stream-1]   WARN c.s.o.l.p.AbstractLogMinerEventProcessor - DDL ALTER TABLE TESTFF1.TABLE_CHAR SHRINK SPACE; has been filtered by filterDdlPattern.
```

Scenario 2: Filter out incremental DDLs from Oracle triggers, stored procedures, etc.

- Problem: DMC currently lacks the ability to parse DDLs, which may lead to risks of interrupting the synchronization link.

- Step 1: Configure task configuration file task_config.yml

```yaml
source: # sink connector option
  type: Oracle # sink connector type
  dataSource:
    hostname:
      - host:  # Oracle monitor ip or hostname
        port:  # Oracle monitor port
    linkType: serviceName # sid or serviceName
    database:  # sid or serviceName value
    pdbName: 
    username:  # link userName
    password: 
  options:
      connectorParameter:
        _filterDdlPattern:
            - ^(?i)CREATE\s+TRIGGER.*
            - ^(?i)CREATE\s+PROCEDURE.*
            - ^(?i)CREATE\s+VIEW.*
```

- Step 2: Start the task and verify

```shell
# YDS Client
YDS@8100> run

# Oracle Database
# Assuming the task is configured to synchronize tables under TESTFF1 user
CREATE VIEW TESTFF1.TABLE_CHAR_VIEW AS SELECT * FROM TESTFF1.TABLE_CHAR;

# Check task logs (run.log) output example:
2025-07-02 15:25:57.835 [oracle-stream-1]   WARN c.s.o.l.p.AbstractLogMinerEventProcessor - DDL CREATE VIEW TESTFF1.TABLE_CHAR_VIEW as select * from TESTFF1.TABLE_CHAR; has been filtered by filterDdlPattern.
```

##### Incremental Phase Error 'IllegalStateException:None of log files contains offset SCN:xxxx, re-snapshot is required.'

This exception may occur during the full incremental synchronization when a snapshot is queried and the SCN is recorded, updating the current recorded SCN for incremental synchronization. If the SCN is found to be missing during synchronization, it may be due to archived log files being purged. It is recommended to set a sufficient retention period for archived log files through RMAN:

```sql
CONFIGURE RETENTION POLICY TO RECOVERY WINDOW OF 7 DAYS;
```

##### Log Continuously Prints "PAUSED FOR FLOW CONTROL"

This situation is usually caused by insufficient configuration of the streams_pool_size on the Oracle server. It is necessary to increase this parameter, which can be checked by executing "SELECT * FROM v$parameter WHERE name = 'streams_pool_size'".

##### How to Connect to Oracle Database Using SYS User

Taking Oracle 19c as an example, refer to [8.1.6 About Using Roles for SYS Login](https://docs.oracle.com/en/database/oracle/oracle-database/19/jjdbc/data-sources-and-URLs.html#GUID-E5230C0F-E22E-42BD-A221-F48D8C8B184C), when logging in as SYS, there are two options.

1. Configure the advanced connection parameters on the source end `source.dataSource.properties`, adding the connection parameter `internal_logon` set to `SYSDBA`, for example:

```yaml
source:
  type: Oracle
  dataSource:
    hostname:
      - host: localhost
        port: 1521
    linkType: serviceName
    database: ORCLCDB
    username: SYS
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==
    properties:
      internal_logon: SYSDBA
```

2. Set the username to `SYS AS SYSDBA`, for example:

```yml
source:
  type: Oracle
  dataSource:
    hostname:
      - host: localhost
        port: 1521
    linkType: serviceName
    database: ORCLCDB
    username: SYS AS SYSDBA
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==
```

After successful configuration, run DMC to connect to the Oracle database using the SYS user.

By default, the SYSDBA user in Oracle cannot log in remotely for security. You can set the Oracle parameter `remote_login_passwordfile` to `exclusive` or `shared`.

```sql
SELECT VALUE FROM V$PARAMETER WHERE NAME = 'remote_login_passwordfile';
ALTER SYSTEM SET remote_login_passwordfile = EXCLUSIVE scope=spfile;
```

Additionally, ensure that the Oracle password file exists; otherwise, the login might be insufficient in privileges. You can use Oracle's `orapwd` command to rebuild the password file.

##### Full Phase Error 'query table empty failed: ORA-01466: unable to read data - table definition has changed'

During the early stages of the full phase, if a query checks whether a table is empty by executing 'SELECT 1 FROM TABLE_NAME AS OF SCN 2313735' flashback query, it may return an error. This situation is rare, and it is recommended to remove the task and run it again for a retry.

##### Incremental Phase Encounter 'OStream-0100 Unexpected exceptions: None of log files contains offset SCN: -1'

This is due to a rare instance where querying the START_SCN field of SYS.GV_$TRANSACTION returns 0. It is recommended to remove the task and retry again.

##### Incremental Phase Encounter 'YAS-00008 type convert error : not a valid year at batch index 3'
During incremental data synchronization, when processing records that are near the boundary values of date types (such as 9999-12-31 23:59:23), there is a risk of data precision loss and type conversion exceptions due to the complexities of JDBC drivers, time zone conversions, and date type boundary handling.
