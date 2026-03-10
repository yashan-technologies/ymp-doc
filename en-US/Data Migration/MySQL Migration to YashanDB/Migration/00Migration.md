The migration phase is used to execute the entire process of data extraction, transformation, and insertion from the source side to the target side. This migration phase is a full data migration.

YMP provides a graphical interaction guide for various configurations, monitoring, exception handling, and other user interfaces required during the full data migration process. Users can also refer to the following documentation for guidance:

- [Migration Configuration](Migration Configuration): Predefine various selection strategies for the migration task during execution. The system has default values, and users can customize it according to their situation.
- [Precheck](Precheck): Users can independently select projects and perform a series of pre-checks before migration to address potential issues that may cause migration failure in advance.
- [Data Migration](Data Migration): Execute migration based on the configurations and strategies set.

### Migration Optimization Suggestions

- **Batch Migration**: For large databases, it is recommended to migrate in batches based on business modules or table sizes.
- **Table Structure Optimization**: Consider optimizing the table structure during the migration process, such as adding necessary indexes or partitions.
- **Estimate Migration Time**: Reasonably estimate migration duration based on data volume and network bandwidth, scheduling it during off-peak business periods.