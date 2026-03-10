

YMP supports the following combination types of migration operations, and you can create tasks as needed:

|Migration Step |Description |
| -------- | -------------------------------------- |
| Assessment              | Performs only the assessment operation; can choose online or offline assessment           |
| Migration               | Performs only the migration operation; no compatibility assessment of database objects has been conducted, and incompatibility may occur during the migration process   |
| Verification            | Performs only the verification operation            |
| Online Assessment + Migration | Performs both assessment and migration operations       |
| Migration + Verification | Performs both migration and verification operations; no compatibility assessment of database objects has been conducted, and incompatibility may occur during the migration process |
| Online Assessment + Migration + Verification | Default procedure for complete migration operations   |
| Configuration Parameters | Can be combined with the above steps for configuration parameter migration operations                |

The main difference between online assessment and offline assessment is that the results of offline assessment cannot be used for subsequent migration stages. Therefore, for tasks requiring migration, online assessment should be selected.

**Online assessment is only available when the source is DB2.**

The following is the input item description when creating a migration task:

|Field |Description |
| ------------ | ------------------------------------------------ |
| Task Name             | Cannot duplicate an existing task name              |
| Migration Step        | Supports the complete migration process; select as needed |
| Assessment Type       | Online assessment, default assessment method; it is recommended that the source be kept silent and offline |
| Source Database       | Select DB2                                          |
| Target Database       | Select YashanDB                                     |
| Remarks               | A brief description of the task                     |

Both **[ create task ]** and **[ Next: Continue Configuration ]** will check whether the Source Database connection user has the necessary Privileges for the selected task steps; missing Privileges will trigger a popup alert.


