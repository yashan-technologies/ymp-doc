##### YashanDB's YStream reports "YDS-0004 Client net error: timeout" when the DMC executes the remove command during large transactions.

The reason is that YStream requires analysis to handle large transaction scenarios. Please be patient, and you may also refer to the related YStream documentation for views to seek assistance.

##### Explanation of incremental DDL synchronization for new columns with default values like varchar2(200) default 'as'||'df' is not supported.

For an add column such as varchar2(200) default 'as'||'df', existing data with the new column will lead to data inconsistency.

##### Explanation of incremental DDL synchronization for statements like alter table t\_part\_descIdx\_col\_001 truncate partition p1 is not supported.

For incremental DDL synchronization like alter table t_part_descIdx_col_001 truncate partition p1, there will be no error interception, which will lead to data inconsistency.

##### Encountering a situation where the time type field on the target side has a one-hour difference compared to the source side during the incremental phase.

Some countries and regions implement daylight saving time and standard time systems. The SCN that DMC actually synchronizes is consistent, but the level of support for heterogeneous data sources may vary, leading to time discrepancies during queries.