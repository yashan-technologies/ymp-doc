The evaluation phase is used to check the compatibility of metadata between the source and target ends. YMP assesses compatibility for heterogeneous databases through object dimensions. During the evaluation process, the DDL statements for creating objects may be modified to some extent.

YMP provides two evaluation methods: online evaluation and offline evaluation. The metadata from the online evaluation can serve as input for the source information in the subsequent migration phase, while the offline evaluation can only be used to execute evaluation tasks.

For PostgreSQL as the source, only online evaluation is allowed.

When using online evaluation, it is recommended that the source remain in a silent offline state to ensure data consistency.