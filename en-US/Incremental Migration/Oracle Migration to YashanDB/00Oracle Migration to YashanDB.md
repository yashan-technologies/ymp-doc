DMC supports online migration from Oracle Database to YashanDB, including full data synchronization and incremental data synchronization.

It is recommended to perform full data synchronization when there is less business on the source side to reduce the impact on online business and obtain a better user experience.

Please read the migration guidance in the order listed in this manual before starting the migration task to avoid unexpected errors.

## Full Concurrency Synchronization Principles

During full data synchronization, to increase synchronization efficiency, the tool uses concurrent query synchronization not only between tables but also within tables (concurrent split table synchronization), with the following specific steps:

1. The tool will automatically determine suitable tables (approximately above 32MB) for concurrent split table synchronization based on statistics;
2. If the above conditions are met, it will check if it is a partitioned table; partitioned tables will be synchronized concurrently based on partition and subpartition information;
3. Otherwise, it will perform concurrent synchronization based on Rowid ranges;
4. If the table is an indexed-organized table (IOT table), it will be synchronized sequentially;
5. If the query statement for the split table fails (due to insufficient privileges for querying DBA_EXTENTS, DBA_OBJECTS, DBA_TAB_SUBPARTITIONS, DBA_TAB_PARTITIONS views or incorrect query information), it will degrade to sequential synchronization for that table.

## DMC Creates Heartbeat Assistant Table on the Source Side

After enabling the heartbeat functionality (when `options.heartbeatEnabled` is true), DMC will create a heartbeat assistant table on the source side named $YDS_HEARTBEAT_INSTANCE_NAME.

If it is necessary to delete the heartbeat assistant table later, the user must delete it manually.