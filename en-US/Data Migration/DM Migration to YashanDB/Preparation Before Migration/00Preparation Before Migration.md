Before performing a full migration, please first understand the information presented in this document and prepare according to the guidance provided in the following documents:

- [data source management](../../../Platform Operation and Maintenance/Data Source Management), mandatory operation.
- [Migration Task Creation](Migration Task Creation), mandatory operation.

## Source Database

- Evaluate source database: Assess the existing DM system, including analyzing database architecture, access patterns, and business requirements, and ensure that the database is in a static offline state.

- Confirm migration scope: Clearly define the database objects to be migrated, including key information such as object scope and data volume.

- During assessment and migration, if the number of connections to the source database increases, it may compete for connection resources and memory. You may adjust the number of available connections as needed.

## Target Database

- Please ensure the target database is installed and operates normally. If not installed, refer to [YashanDB Installation and Deployment](https://doc.yashandb.com/yashandb/23.4/zh/Installation-and-Upgrade/Installation-and-Deployment/00Installation-and-Deployment.html).

- Please ensure that there is 120% space reserved in the target for the data volume from the source.

## YMP

- Please ensure YMP is installed and operates normally. If not installed, refer to [YMP Installation and Uninstallation](../../../Installation and Upgrade/Installing and Uninstalling/00Installing and Uninstalling). This section assumes using the built-in library for YMP installation.

- Recommended network bandwidth: >= 100Mbps.

## Environment Description

DM and YashanDB example environment description:

|Database Side |Database Type |Version |Address |Database Username |
| -------- | ---------- | ---------- | ------------- | ------------ |
| Source        | DM            | DM8        | 172.16.90.128   | tpch              |
| Target        | YashanDB      | 23.4.1.100 | 172.16.90.58    | SYS               |

YMP example environment description:

|Migration Tool |Version |Address |
| -------- | -------- | ------------ |
| YMP      | 23.4.4.1 | 172.16.90.81 |
