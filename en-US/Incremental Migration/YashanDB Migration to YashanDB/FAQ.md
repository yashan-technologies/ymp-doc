##### The YStream of YashanDB reports "YDS-0004 Client net error: timeout" when executing the remove command during large transactions.

The reason is that YStream's deletion requires analysis of the large transaction situation. Please be patient. Meanwhile, you can refer to the relevant documentation of YStream to check relevant views for assistance.

##### Incremental DDL synchronization for adding a new column with a default value like varchar2(200) default 'as'||'df' is not supported.

For an add column operation like varchar2(200) default 'as'||'df', the new column will cause data inconsistency with existing data.

##### Incremental DDL synchronization for operations like alter table t\_part\_descIdx\_col\_001 truncate partition p1 is not supported.

For the incremental DDL synchronization such as alter table t_part_descIdx_col_001 truncate partition p1, there will be no error interception, which will lead to data inconsistency.