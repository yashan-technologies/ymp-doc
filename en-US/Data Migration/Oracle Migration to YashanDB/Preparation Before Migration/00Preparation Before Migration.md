Before proceeding with a full migration, please understand the information presented in this document and prepare according to the following guidelines:

- [ data source management](../../../Platform Operation and Maintenance/Data Source Management), must be performed.
- [Migration Task Creation](Migration Task Creation), must be performed.

## Source Database

- Evaluate the source database: Assess the existing Oracle system, including analysis of the database schema, access patterns, and business requirements, and ensure that the database is in a static offline state.

- Confirm the migration scope: Clarify the database objects that need to be migrated, including key information such as the range of objects and data volume.

- During evaluation and migration, an increase in connections to the source database may compete for connection resources and memory. You can adjust the available number of connections as needed.

## Target Database

- Please ensure that the target database is installed and running correctly. If not installed, refer to [YashanDB Installation and Deployment](https://doc.yashandb.com/yashandb/23.4/zh/Installation-and-Upgrade/Installation-and-Deployment/00Installation-and-Deployment.html).

- Please ensure that 120% of the source data volume space is reserved on the target side.

## YMP

- Please ensure YMP is installed and running correctly. If not installed, refer to [YMP Installation and Uninstallation](../../../Installation and Upgrade/Installing and Uninstalling/00Installing and Uninstalling). This section assumes YMP is installed using the built-in library.

- Network bandwidth (recommended): >= 100Mbps.

## Environment Description

Description of Oracle and YashanDB example environments:

|Database Side |Database Type |Version |Address |Database User |
| -------- | ---------- | --------- | ------------- | ------------ |
| Source Side   | Oracle        | 19.0.0.0  | 172.16.90.118  | ymp            |
| Target Side   | YashanDB      | 23.2.11.1  | 172.16.90.173  | ymp            |

Description of YMP example environment:

|Migration Tool |Version |Address |
| -------- | -------- | ------------ |
| YMP      | 23.4.4.1 | 172.16.90.81 |
