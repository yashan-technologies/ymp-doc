The migration phase is used to execute the entire process of extracting, transforming, and inserting data from the source to the target. This migration phase is a full data migration.

YMP provides a user interface guide for various configurations, monitoring, and exception handling required during the full data migration process. Users can also refer to the following documentation for guidance:

- [Migration Configuration](Migration Configuration): Predefine various selection strategies for the migration task during execution. The system has default values, but users can customize them based on their situations.
- [Precheck](Precheck): Users can independently select projects and perform a series of pre-checks before migration to address potential issues that may cause migration failure in advance.
- [Data Migration](Data Migration): Execute migration based on the strategies defined in the migration configuration.

### Migration Optimization Recommendations

- **Batch Migration**: For large databases, it is recommended to migrate in batches according to business modules or table sizes.
- **Table Structure Optimization**: Consider optimizing the table structure during the migration process, such as adding necessary indexes or partitions.
- **Estimate Migration Time**: Reasonably estimate migration duration based on data volume and network bandwidth, scheduling during business low peaks.