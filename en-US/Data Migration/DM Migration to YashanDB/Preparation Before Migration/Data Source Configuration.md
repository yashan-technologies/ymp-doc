

For detailed data source basic configuration, please refer to [data source management](../../../Platform Operation and Maintenance/Data Source Management).

To perform periodic verification, both the source and target users must have the FLASHBACK ANY TABLE privilege.



## Source DM Authorization

### Privilege Configuration for Regular Users

Regular users are suitable for scenarios that only involve migrating objects and data under this user. Regular users only require minimal privileges. However, there are the following limitations:

- Migrated objects cannot depend on objects from other schemas. For example, migrating a view object that queries tables from other schemas.
- Validations during initialization regarding high-level configurations have limitations; performance configuration checks cannot determine whether the available connections on the source side meet the user's configured number of connections.
- Regular DM users do not require additional authorization.

### Privilege Configuration for Non-Regular Users

To ensure a smooth migration, you can directly grant DBA privileges to the user.

Or grant the following permissions, with the authorization command as follows:

:::tabs

== Evaluation

```sql
GRANT SOI TO username;
GRANT SELECT ON DBA_USERS TO username;
GRANT SELECT ON DBA_OBJECTS TO username;
GRANT SELECT ON DBA_INDEXES TO username;
GRANT SELECT ON DBA_VIEWS TO username;
GRANT SELECT ON DBA_CONSTRAINTS TO username;
GRANT SELECT ON DBA_TAB_COLS TO username;
GRANT SELECT ON DBA_TAB_COMMENTS TO username;
GRANT SELECT ON DBA_COL_COMMENTS TO username;
GRANT SELECT ON DBA_IND_COLUMNS TO username;
GRANT SELECT ON DBA_SEGMENTS TO username;
GRANT SELECT ON DBA_TRIGGERS TO username;
GRANT SELECT ON DBA_TABLES TO username;
GRANT SELECT ON DBA_TAB_PARTITIONS TO username;
GRANT SELECT ON V$PARAMETER TO username;
```

== Migration

```sql
GRANT SELECT ON DBA_OBJECTS TO username;
GRANT SELECT ON DBA_SEGMENTS TO username;
GRANT SELECT ON DBA_DATA_FILES TO username;
GRANT SELECT ON DBA_TAB_COLS TO username; -- Recommended PRIVILEGE, NOT mandatory; pre-checking doesn't require this IF TABLE risk types are NOT checked
GRANT SELECT ANY TABLE TO username; -- Recommended PRIVILEGE, NOT mandatory; can be replaced BY SELECT ON OF migrated tables
GRANT SELECT ANY SEQUENCE TO username; -- Recommended PRIVILEGE, NOT mandatory; can be replaced BY SELECT ON OF migrated sequences
```

== Validation

```sql
GRANT SELECT ON DBA_OBJECTS TO username;
GRANT SELECT ON V$SESSIONS TO username;
GRANT SELECT ON DBA_SEGMENTS TO username;
GRANT SELECT ON DBA_TABLES TO username;
GRANT SELECT ON DBA_TAB_COLUMNS TO username;
GRANT SELECT ON DBA_CONS_COLUMNS TO username;
GRANT SELECT ON DBA_CONSTRAINTS TO username;
GRANT SELECT ANY TABLE TO username; -- Suggested permissions, not mandatory.
```

:::

## Target YashanDB Authorization

The authorization commands are as follows:

:::tabs

== YashanDB

```sql
GRANT DBA TO username;
```

== YashanDB (Enabling Separation of Duties)

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
