The migration phase is used to perform the entire process of data extraction, transformation, and insertion from the source to the target, and this migration phase is a full data migration.

YMP provides a user interface for various configurations, monitoring, and exception handling required during the full data migration process, and users can also refer to the following documentation for guidance:

- [Migration Configuration](Migration Configuration): This defines various selection strategies for the migration task during execution, with the system providing default values, which users can customize based on their own situation.
- [Precheck](Precheck): Users can independently select projects and execute a series of prechecks before migration to address potential issues that may cause migration failure.
- [Data Migration](Data Migration): Executes migration based on the strategies defined in the migration configuration.

### Migration Optimization Suggestions

- **Batch Migration**: For large databases, it is recommended to migrate in batches based on business modules or table sizes.
- **Table Structure Optimization**: Consider optimizing the table structure during migration, such as adding necessary indexes or partitions.
- **Estimated Migration Time**: Reasonably estimate migration duration based on data volume and network bandwidth, scheduling it during off-peak business hours.