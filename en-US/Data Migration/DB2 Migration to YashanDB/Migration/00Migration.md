The migration phase is used to execute the entire process of extracting, transforming, and inserting data from the source to the target. This migration phase is a full data migration.

YMP provides a graphical user interface for various configurations, monitoring, and exception handling required during the full data migration process. Users can also refer to the following documentation for guidance:

- [Migration Configuration](Migration Configuration): Predefining various selection strategies for the migration task during execution. The system has default values, and users can customize based on their situation.
- [Precheck](Precheck): Users can autonomously select projects and perform a series of pre-checks before migration to address potential issues that might cause migration failure in advance.
- [Data migration](Data migration): Executing the migration according to the migration configuration and strategies.

### Migration Optimization Recommendations

- **Batch Migration**: For large databases, it is recommended to migrate in batches based on business modules or table sizes.
- **Table Structure Optimization**: Consider optimizing table structures during migration, such as adding necessary indexes or partitions.
- **Estimated Migration Time**: Based on data volume and network bandwidth, reasonably estimate migration duration and schedule it during low business peaks.