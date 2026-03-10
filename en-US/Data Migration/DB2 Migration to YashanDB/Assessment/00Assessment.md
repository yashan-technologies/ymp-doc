The evaluation phase is used to check the compatibility of metadata between the source and target ends. YMP performs compatibility assessments for heterogeneous databases through object dimensions. During the assessment process, there may be a certain degree of rewriting of the DDL statements for creating objects.

YMP provides two evaluation methods: online evaluation and offline evaluation. In online evaluation, the metadata can serve as input for source information in the subsequent migration phase. Offline evaluation, on the other hand, can only be used to execute evaluation tasks.

For DB2 source ends, only online evaluation is permitted.

When using online evaluation, it is recommended that the source end remains in a silent offline state to ensure data consistency.