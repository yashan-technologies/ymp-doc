##### 1. After task migration, the verification task cannot be started.

The verification task only supports verification of successfully completed data migration tasks. The verification object inherits from the migration object, with the default data migration success table as the verification table and others as non-verification tables.

##### 2. When creating a verification task, an error occurs: "No verification table object."

The verification task did not select a verification object; a verification object should be added; otherwise, the verification is meaningless.

##### 3. When creating a verification task, an error occurs: "If this task is started, the number of connections may exceed the maximum connection parameter limit configured by YDC, please check the configuration to avoid errors."

Starting the verification task may occupy the same number of connections as the table's concurrency. If the sum of the connections currently being used by the verification module and the connections required for creating this task exceeds the maximum number of connections in the connection pool, it may prevent the task from acquiring connections correctly. You should wait for some of the currently used connections to be released before starting the task again.

##### 4. When creating a verification task, an error occurs: "The current Data Source XXX cannot provide enough connections to support the concurrency of this task at this time; please check the database to avoid errors."

The verification task detected that the data source cannot provide the maximum number of connections required for the verification task, which may lead to errors in the verification task. The user needs to release some database connections for verification use.

##### 5. During or after the verification, why are some tables in a skipped state?

The verification task detected unsupported tables and set them to a skipped state. You can click to view the details to check the reason for skipping.

##### 6. MySQL database does not guarantee the correctness of verification for character-type primary keys storing the following special characters;

Character-type primary key values: #, ", `, \n, ,, /, /n, etc.

##### 7. The source side is varchar (or nvarchar) data type, and the target side is char (or nchar) data type. Verification data is inconsistent?

The database automatically pads spaces on the right side of char (or nchar) data, leading to inconsistencies in data validation. You can set checkTask.varcharRightTrim=true in the YMP configuration file application.properties; the functionality of this configuration is that for verification tasks, if the source side is varchar (or nvarchar) data type, it will remove trailing spaces from both the source and target data for comparison.

##### 8. The source side is MySQL's decimal, and the target side is YashanDB's NUMBER type. If the migrated value exceeds YashanDB, the verification will fail, ensuring the user is aware of this situation;

```sql
--mysql, the queried result is still the original value inserted
CREATE TABLE tb_deciaml_002 (id INT PRIMARY key,c1 DECIMAL(65,30));
INSERT INTO tb_deciaml_002 VALUES(1,99999999999999999999999999999999999.999999999999999999999999999999);

--yashandb, after migration, the queried displayed value has changed to: 100000000000000000000000000000000000
CREATE TABLE "YANGM"."TB_DECIAML_002"("ID" INTEGER,"C1" NUMBER DEFAULT NULL);
INSERT INTO TB_DECIAML_002(ID, C1) VALUES(0, 99999999999999999999999999999999999.999999999999999999999999999999);
```

##### 9. Verification failed, error message shows - pump initialization exception, initialization sql: SQL statement, exception information: ORA-01652: unable to extend temp segment by 128 in tablespace TEMP.

The Oracle data query statement occupies the temporary tablespace, causing insufficient temporary tablespace. It is recommended to avoid this by using the following methods:

- Increase the temporary tablespace.

```sql
-- Query the state of tablespaces
SELECT tablespace_name,ROUND((bytes / (1024 * 1024)), 2) AS size_mb,ROUND((maxbytes / (1024 * 1024)), 2) AS max_size_mb  \
FROM dba_temp_files;
-- Increase the temporary tablespace
ALTER DATABASE TEMPFILE 'tempfile_path' RESIZE new_size;
```

- Clean up the temporary tablespace.
- Reduce table verification concurrency.

##### 10. When renaming the schema during evaluation, case insensitivity does not take effect, and the corresponding table cannot be found.

When renaming the schema during evaluation, for objects with modified schema names, the verification matching will strictly follow the modification rules. For other non-renamed schema objects, match according to whether the case-insensitivity rule is checked.

##### 11. Verification task failed, log shows error: ORA-02032: cluster tables cannot be used before the cluster index is built.

Oracle states that cluster tables cannot be used before a cluster index is established. You can exclude cluster tables that do not have an established cluster index or create a cluster index for cluster tables that have not established one.

##### 12. In the process of terminating the DM full verification, an error message appears: "Source Database connection user dm does not have sufficient Privilege to execute SP\_CANCEL\_SESSION\_OPERATION(session\_id bigint) on the current session."

Only DBA users in DM can terminate tasks. 

To terminate DM full verification, please GRANT DBA TO USER or execute SELECT * FROM V$SESSIONS to find the SESS_ID in the database (ensure the SESS_ID found is the ID used for the YMP verification task; the ID used for the verification task usually has SQL_TEXT as the query SQL; STATE as ACTIVE; USER_NAME consistent with the data source user added by YMP; CREATE_TIME after the execution of the verification task; CLNT_TYPE as JDBC; CLNT_HOST as the machine where YMP is located; RUN_STATUS as RUNNING; MSG_STATUS as RECEIVE. 

If you cannot accurately determine, please seek assistance from YMP developers). Then execute SP_CANCEL_SESSION_OPERATION(SESS_ID bigint) to kill the session, and the full verification shows the task failed.

##### 13. MySQL to Yashan verification, both sides are "", and the verification result is inconsistent.

Check the setting of emptyStringConvertToNull, which will treat the source "", as NULL compared to the target side. It is recommended to change emptyStringConvertToNull to false when the Yashan database configuration EMPTY_STRING_AS_NULL=false.

##### 15. When verifying between MySQL and Yashan, an error occurs: The last packet successfully received from the server was a segment of time milliseconds ago.

If the MySQL database sorting takes too long, the connection becomes idle for an extended period, causing MySQL to close the connection automatically. When trying to use this connection again, an error occurs. You can adjust the concurrency of large table splitting and reduce the number of splits to alleviate the pressure on MySQL. It is recommended to reduce the concurrency. Alternatively, optimize MySQL performance by increasing the MySQL wait_timeout parameter.

##### 16. During verification, an error occurs on the YashanDB source: "YAS-04454 not enough threads for parallel execution" resulting in verification failure.

The problem may arise because the table on YashanDB has the PARALLEL XXX table creation attribute, causing a parallel scan when querying the table, which exceeds the resources configured by the database for MAX_PARALLEL_WORKERS, leading to an error. 

Resolution: If this error occurs, check the PARALLEL property value of the table and increase the MAX_PARALLEL_WORKERS value in the YashanDB database. Retrying the verification using single table verification can effectively avoid verification failures caused by concurrent multiple tables.