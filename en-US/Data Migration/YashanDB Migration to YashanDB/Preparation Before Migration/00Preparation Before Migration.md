Before performing a full migration, please understand the information presented in this document and prepare according to the following documents:

- [Data Source Management](../../../Platform Operation and Maintenance/Data Source Management), must operate.
- [Migration Task Creation](Migration Task Creation), must operate.

## Source Database

- Evaluate source database: Assess the existing YashanDB system, including analyzing database architecture, access patterns, and business requirements, and ensure the database is in a static offline state.

- Confirm migration scope: Specify the database objects that need to be migrated, including key information such as object scope and data volume.

- During evaluation and migration, if the source database connections increase, they will compete for connection resources and memory. You can adjust the number of available connections as needed.

## Target Database

- Please ensure that the target database is installed and operating normally. If not installed, please refer to [YashanDB Installation](https://doc.yashandb.com/yashandb/23.4/zh/Installation-and-Upgrade/Installation-and-Deployment/00Installation-and-Deployment.html).

- Please ensure that the target side reserves 120% of the source side's data volume in space.

## YMP

- Please ensure that YMP is installed and operating normally. If not installed, please refer to [YMP Installation](../../../Installation and Upgrade/Installing and Uninstalling/00Installing and Uninstalling). This section assumes YMP is installed using the built-in library.

- Network bandwidth (recommended): >= 100Mbps.

## Environment Description

YashanDB, YashanDB example environment description:

|Database Side |Database Type |Version |Address |Database Username |
| -------- | ---------- | --------- | ------------ | ------------ |
| Source Side   | YashanDB      | 23.4.0.42  | 172.16.90.76    | SYS                |
| Target Side   | YashanDB      | 23.4.0.42  | 172.16.90.58    | SYS                |

YMP example environment description:

|Migration Tool |Version |Address |
| -------- | -------- | ------------ |
| YMP      | 23.4.5.2 | 172.16.90.81 |
