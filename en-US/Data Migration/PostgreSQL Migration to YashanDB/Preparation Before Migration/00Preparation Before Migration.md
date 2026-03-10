Before performing a full migration, please understand the information presented in this document and prepare according to the guidance provided in the following documents:

- [data source management](../../../Platform Operation and Maintenance/Data Source Management), mandatory operation.
- [Migration Task Creation](Migration Task Creation), mandatory operation.

## Source Database

- Evaluate the source database: Assess the existing PG system, including analyzing the database architecture, access patterns, and business requirements, and ensure the database is in a static offline state.

- Confirm the migration scope: Clearly identify the database objects that need to be migrated, including key information such as the scope of objects and data volume.

- During the migration, as the connections to the source database increase, it may compete for connection resources and memory. You can adjust the available number of connections as needed.

## Target Database

- Please ensure that the target database is installed and is in a normal running state. If not installed, refer to [YashanDB Installation and Deployment](https://doc.yashandb.com/yashandb/23.4/zh/Installation-and-Upgrade/Installation-and-Deployment/00Installation-and-Deployment.html).

- Please ensure that the target database has reserved 120% of the source data volume space.

## YMP

- Please ensure that YMP is installed and is in a normal running state. If not installed, refer to [YMP Installation and Uninstallation](../../../Installation and Upgrade/Installing and Uninstalling/00Installing and Uninstalling). This section assumes YMP is installed using the built-in library.

- Recommended network bandwidth: >= 100Mbps.

## Environment Description

PG and YashanDB example environment description:

|Database Side |Database Type |Version Number |Address |Database Username |
| -------- | ---------- | ----------- | ------------- | ------------ |
| Source Side   | PG             | 90602          | 172.16.90.103    | postgres          |
| Target Side   | YashanDB       | 22.2.12.100    | 172.16.90.151    | sys               |

YMP example environment description:

|Migration Tool |Version Number |Address |
| -------- | --------- | ------------ |
| YMP      | 23.4.11.1 | 172.16.90.25 |
