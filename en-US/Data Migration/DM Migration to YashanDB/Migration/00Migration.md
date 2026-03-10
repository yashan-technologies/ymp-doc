The migration phase is used to execute the entire process of extracting, transforming, and loading data from the source to the target. This migration phase is a full data migration.

YMP provides a graphical interaction guide for various configurations, monitoring, and exception handling required during the full data migration process. Users can also refer to the following documents for guidance:

- [Migration Configuration](Migration Configuration): This document defines various selection strategies for the migration task during execution. The system has default values, and users can customize them based on their needs.
- [Precheck](Precheck): Users can independently select projects to perform a series of pre-checks before migration, addressing issues that may cause migration failures in advance.
- [Data Migration](Data Migration): Execute the migration according to the selected strategies in the migration configuration.

### Migration Optimization Suggestions

- **Batch Migration**: For large databases, it is recommended to migrate in batches according to business modules or table sizes.
- **Table Structure Optimization**: Consider optimizing table structures during migration, such as adding necessary indexes or partitions.
- **Estimate Migration Time**: Reasonably estimate migration duration based on data volume and network bandwidth, and schedule during business off-peak periods.