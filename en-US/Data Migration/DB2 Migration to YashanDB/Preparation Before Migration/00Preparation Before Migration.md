Before performing a full migration, please familiarize yourself with the information presented in this document and prepare according to the guidance in the following documents:

- [ Data Source Management](../../../Platform Operation and Maintenance/Data Source Management), must operate.
- [Migration Task Creation](Migration Task Creation), must operate.

## Source Database

- Assess source database: Evaluate the existing DB2 system, including analyzing the database architecture, access patterns, and business requirements, and ensure that the database is in a static offline state.

- Confirm migration scope: Clearly define the database objects to be migrated, including key information such as object scope and data volume.

- As the number of connections to the source database increases during migration, there may be contention for connection resources and memory. You can adjust the available connection count as needed.

## Target Database

- Please ensure that the target database is installed and operating normally. If not installed, refer to [YashanDB Installation and Deployment](https://doc.yashandb.com/yashandb/23.4/zh/Installation-and-Upgrade/Installation-and-Deployment/00Installation-and-Deployment.html).

- Please ensure that 120% of the source data volume is reserved in the target environment.

## YMP

- Please ensure that YMP is installed and operating normally. If not installed, refer to [YMP Installation and Uninstallation](../../../Installation and Upgrade/Installing and Uninstalling/00Installing and Uninstalling); this section assumes the use of the built-in library to install YMP.

- Recommended network bandwidth: >= 100Mbps.

## Environment Description

DB2 and YashanDB example environment description:

|Database Side |Database Type |Version |Address |Database Username |
| ---- | -------- | ---------- | ------------- | ------ |
| Source         | DB2           | v10.5.0.11 | 172.16.90.74   | ymp                |
| Target         | YashanDB      | 23.4.2.100 | 172.16.90.151  | sys                |

YMP example environment description:

|Migration Tool |Version |Address |
| ---- | --------- | ---------------------------------------- |
| YMP  | 23.4.10.1 | 172.16.90.25 |
