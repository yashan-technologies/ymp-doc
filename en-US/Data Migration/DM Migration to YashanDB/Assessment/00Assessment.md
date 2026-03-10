The assessment stage is used to check the compatibility of metadata between the source and target ends. YMP evaluates the compatibility of heterogeneous databases through object dimensions, and during the evaluation process, the DDL statements for creating objects may be modified to a certain extent.

YMP provides two assessment methods: online assessment and offline assessment. In online assessment, the metadata can serve as the source-side information input for the subsequent migration phase; while offline assessment can only be used for executing evaluation tasks.

When using online assessment, it is recommended that the source side remain in a silent offline state to ensure data consistency.