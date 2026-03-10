The migration stage is used to execute the entire process of extracting, transforming, and loading data from the source to the target, and this migration phase is a full data migration.

YMP provides a graphical interface for various configurations, monitoring, and error handling required during the full data migration process. Users can also follow the guidance in the following documents for operation:

- [Migration Configuration](Migration Configuration): Pre-defines various selection strategies for migration tasks during execution. The system has default values, but users can customize them based on their own situations.
- [Precheck](Precheck): Users can select projects to independently perform a series of pre-checks before migration, addressing potential issues that may cause migration failure in advance.
- [Data Migration](Data Migration): Executes the migration based on the strategies defined in the migration configuration.

### Migration Optimization Recommendations

- **Batch Migration**: For large databases, it is recommended to migrate in batches by business module or table size.
- **Table Structure Optimization**: Consider optimizing the table structure during migration, such as adding necessary indexes or partitions.
- **Estimate Migration Time**: Reasonably estimate the migration duration based on data volume and network bandwidth, and schedule it during low business peak times.