Before performing a full migration, please understand the information presented in this document and prepare according to the following documents:

- [Data Source Management](../../../Platform Operation and Maintenance/Data Source Management), mandatory operation.
- [Migration Task Creation](Migration Task Creation), mandatory operation.

## Source Database

- Evaluate the source database: Assess the existing MySQL system, including analyzing the database architecture, access patterns, and business requirements, and ensure that the database is in a static offline state.

- Confirm the migration scope: Identify the database objects that need to be migrated, including key information such as the scope of the objects and the data volume.

- During evaluation and migration, the number of connections to the source database may increase, competing for connection resources and memory. You can adjust the available number of connections as needed.

## Target Database

- Please ensure that the target database is installed and running normally. If it is not installed, please refer to [YashanDB Installation](https://doc.yashandb.com/yashandb/23.4/zh/Installation-and-Upgrade/Installation-and-Deployment/00Installation-and-Deployment.html).

- Please ensure that 120% of the source data volume is reserved on the target side.

## YMP

- Please ensure that YMP is installed and running normally. If it is not installed, please refer to [YMP Installation](../../../Installation and Upgrade/Installing and Uninstalling/00Installing and Uninstalling), this chapter assumes using the built-in library to install YMP.

- Recommended network bandwidth: >= 100Mbps.

## Environment Description

MySQL and YashanDB example environment description:

|Database Side |Database Type |Version Number |Address |Database Username |
| -------- | ---------- | ---------- | ------------- | ------------ |
| Source Side   | MySQL         | 8.0.23         | 172.16.90.118    | root              |
| Target Side   | YashanDB      | 23.4.1.102     | 172.16.90.58     | SYS               |

YMP example environment description:

|Migration Tool |Version Number |Address |
| -------- | -------- | ------------ |
| YMP      | 23.4.4.1 | 172.16.90.81 |
