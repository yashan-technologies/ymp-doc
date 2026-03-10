

For detailed data source basic configuration, please refer to [data source management](../../../Platform Operation and Maintenance/Data Source Management).

To perform periodic verification, both the source and target users must have the FLASHBACK ANY TABLE privilege.



## Source Oracle Authorization

### Normal User Privilege Configuration

A normal user is suitable for scenarios that only involve migrating objects and data under that user. A normal user only requires the minimum privileges. However, there are the following limitations:

- The migrated objects cannot depend on objects in other schemas. For example: migrating a view object that queries tables in other schemas.
- Verifying advanced configurations in initialization loses the effectiveness of performance configuration checks, thus it cannot determine whether the number of available connections on the source side meets the user-configured number of connections.
- Oracle does not support table splitting for large tables during data migration. The lack of data source for normal users renders the split configuration parameters ineffective.
- Due to insufficient query permissions on necessary views for normal user, Oracle 9i, 10g, and 11g versions cannot perform role initialization during migration.

The privileges required for normal users are:

```sql
GRANT CONNECT TO username;
GRANT RESOURCE TO username;
```

### Non-Normal User Privilege Configuration

By default, the source Oracle is a non-normal user. To ensure smooth migration, please ensure that the source Oracle user has the following privileges:

The authorization commands are as follows:

:::tabs

== Evaluation

```sql
GRANT CREATE SESSION TO username;  
GRANT SELECT_CATALOG_ROLE TO username; 
```

== Migration

> **Note**:
>
> If the "SELECT_CATALOG_ROLE" role is granted during evaluation, any system view can be queried without the need to grant "SELECT ON xxx" permissions in the following ranges.

Only full data migration:

```sql
GRANT CREATE SESSION TO username;
GRANT SELECT ON DBA_OBJECTS TO username;
GRANT SELECT ON DBA_EXTENTS TO username;
GRANT SELECT ON DBA_SEGMENTS TO username;
GRANT SELECT ON DBA_LOBS TO username;
GRANT SELECT ANY TABLE TO username; -- Recommended PRIVILEGE, NOT mandatory, can be replaced BY the SELECT ON PRIVILEGE OF the migrated TABLE
GRANT SELECT ANY SEQUENCE TO username; -- Recommended PRIVILEGE, NOT mandatory, can be replaced BY the SELECT ON PRIVILEGE OF the migrated SEQUENCE
GRANT SELECT ON DBA_TAB_COLS TO username; -- Recommended PRIVILEGE, NOT mandatory, pre-checking does NOT need TO CHECK TABLE risk types
GRANT SELECT ON DBA_SCHEDULER_JOBS TO username; -- Recommended PRIVILEGE, NOT mandatory, pre-checking does NOT need TO CHECK active scheduled tasks
GRANT SELECT ON DBA_MVIEWS TO username; -- Recommended PRIVILEGE, NOT mandatory, pre-checking does NOT need TO CHECK materialized views
```

Full migration + Incremental migration：

If you need to use **[Full Data Migration]** + **[Incremental Data Migration]**, after completing the basic authorization for full migration and incremental migration, you also need to perform the following authorization：

```sql
GRANT FLASHBACK ON DBA_SEGMENTS TO username;
GRANT FLASHBACK ON DBA_EXTENTS TO username;
GRANT FLASHBACK ON DBA_OBJECTS TO username;
GRANT FLASHBACK ON DBA_LOBS TO username;
GRANT FLASHBACK ON ALL_LOBS TO username;
GRANT FLASHBACK ON ALL_TAB_COLS TO username;
GRANT FLASHBACK ON ALL_TABLES TO username;
GRANT FLASHBACK ON ALL_PART_TABLES TO username; 
GRANT FLASHBACK ON ALL_LOB_PARTITIONS TO username;
GRANT FLASHBACK ON ALL_LOB_SUBPARTITIONS TO username;
GRANT FLASHBACK ON ALL_TAB_PARTITIONS TO username;
GRANT FLASHBACK ON ALL_TAB_SUBPARTITIONS TO username;
GRANT LOGMINING TO username;
GRANT SELECT ON DBA_SCHEDULER_JOBS TO username;
```

== Validation

> **Note**:
>
> If the "SELECT_CATALOG_ROLE" role is granted during evaluation, any system view can be queried without the need to grant "SELECT ON xxx" permissions in the following ranges.

```sql
GRANT CREATE SESSION TO username;
GRANT SELECT ON DBA_OBJECTS TO username;
GRANT SELECT ON V_$PARAMETER TO username;
GRANT SELECT ON V_$SESSION TO username;
GRANT SELECT ON DBA_EXTENTS TO username; -- Required FOR Oracle 9i AND 10g
GRANT SELECT ON DBA_SEGMENTS TO username; -- Required FOR other versions
GRANT SELECT ON DBA_TABLES TO username;
GRANT SELECT ON DBA_TAB_COLUMNS TO username;
GRANT SELECT ON DBA_CONS_COLUMNS TO username;
GRANT SELECT ON DBA_CONSTRAINTS TO username;
GRANT SELECT ON DBA_LOBS TO username;
GRANT SELECT ANY TABLE TO username; -- Recommended privilege, not mandatory.
```

== Configuration Parameters

> **Note**:
>
> If the "SELECT_CATALOG_ROLE" role is granted during evaluation, any system view can be queried without the need to grant "SELECT ON xxx" permissions in the following ranges.

```sql
GRANT CREATE SESSION TO username;
GRANT SELECT ON V_$PARAMETER TO username;
```

:::

## Target YashanDB Authorization

The authorization commands are as follows:

:::tabs

== YashanDB

```sql
GRANT DBA TO username;
```

== YashanDB (Enable Separation of Duties)

```sql
GRANT CONNECT TO username WITH ADMIN OPTION;
GRANT RESOURCE TO username WITH ADMIN OPTION;
GRANT ALTER SESSION TO username WITH ADMIN OPTION;
GRANT SELECT_CATALOG_ROLE TO username WITH ADMIN OPTION;
GRANT CREATE USER TO username WITH ADMIN OPTION;
GRANT CREATE ANY TABLE TO username WITH ADMIN OPTION;
GRANT DROP ANY TABLE TO username WITH ADMIN OPTION;
GRANT ALTER ANY TABLE TO username WITH ADMIN OPTION;
GRANT COMMENT ANY TABLE TO username WITH ADMIN OPTION;
GRANT INSERT ANY TABLE TO username WITH ADMIN OPTION;
GRANT DELETE ANY TABLE TO username WITH ADMIN OPTION;
GRANT UPDATE ANY TABLE TO username WITH ADMIN OPTION;
GRANT SELECT ANY TABLE TO username WITH ADMIN OPTION;
GRANT CREATE ANY SEQUENCE TO username WITH ADMIN OPTION;
GRANT DROP ANY SEQUENCE TO username WITH ADMIN OPTION;
GRANT SELECT ANY SEQUENCE TO username WITH ADMIN OPTION;
GRANT CREATE ANY INDEX TO username WITH ADMIN OPTION;
GRANT ALTER ANY INDEX TO username WITH ADMIN OPTION;
GRANT DROP ANY INDEX TO username WITH ADMIN OPTION;
GRANT CREATE ANY SYNONYM TO username WITH ADMIN OPTION;
GRANT CREATE PUBLIC SYNONYM TO username WITH ADMIN OPTION;
GRANT DROP ANY SYNONYM TO username WITH ADMIN OPTION;
GRANT DROP PUBLIC SYNONYM TO username WITH ADMIN OPTION;
GRANT CREATE ANY VIEW TO username WITH ADMIN OPTION;
GRANT DROP ANY VIEW TO username WITH ADMIN OPTION;
GRANT CREATE ANY MATERIALIZED VIEW TO username WITH ADMIN OPTION;
GRANT DROP ANY MATERIALIZED VIEW TO username WITH ADMIN OPTION;
GRANT CREATE ANY TYPE TO username WITH ADMIN OPTION;
GRANT DROP ANY TYPE TO username WITH ADMIN OPTION;
GRANT CREATE ANY TRIGGER TO username WITH ADMIN OPTION;
GRANT DROP ANY TRIGGER TO username WITH ADMIN OPTION;
GRANT ALTER ANY TRIGGER TO username WITH ADMIN OPTION;
GRANT CREATE ANY PROCEDURE TO username WITH ADMIN OPTION;
GRANT DROP ANY PROCEDURE TO username WITH ADMIN OPTION;
GRANT SELECT ON DBA_TABLESPACES TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_DATA_FILES TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_TRIGGERS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_DEPENDENCIES TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_USERS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_OBJECTS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_CONSTRAINTS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_INDEXES TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_ROLE_PRIVS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_SYS_PRIVS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_TABLES TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_TAB_COLUMNS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_CONS_COLUMNS TO username WITH GRANT OPTION;
GRANT SELECT ON DBA_CONSTRAINTS TO username WITH GRANT OPTION;
```

:::
