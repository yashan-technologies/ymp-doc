The assessment phase is used to check the compatibility of metadata between the source and target ends. YMP evaluates compatibility through object dimensions for heterogeneous databases. During the evaluation process, the DDL statements for creating objects may be modified to some extent.

YMP provides two evaluation methods: online assessment and offline assessment. In the online assessment, the metadata can serve as input information for the source end in the subsequent migration phase; while the offline assessment is only used for executing evaluation tasks.

When conducting online assessment, it is recommended to keep the source end in a silent offline state to ensure data consistency.