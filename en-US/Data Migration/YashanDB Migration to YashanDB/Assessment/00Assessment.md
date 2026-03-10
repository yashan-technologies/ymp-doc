The evaluation stage is used to check the compatibility of metadata between the source and target. YMP assesses the compatibility of heterogeneous databases through object dimensions. During the assessment, the DDL statements for creating objects may undergo a certain degree of rewriting.

YMP offers two evaluation methods: online evaluation and offline evaluation. In online evaluation, the metadata can be used as input for the source information in the subsequent migration phase, while offline evaluation can only be used to execute evaluation tasks.

For the YashanDB source, only online evaluation is permitted.

When using online evaluation, it is recommended to keep the source in a silent offline state to ensure data consistency.