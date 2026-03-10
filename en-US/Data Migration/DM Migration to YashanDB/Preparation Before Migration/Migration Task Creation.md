

YMP supports the following combination types of migration operations, and you can create tasks as needed:

|Migration Step |Description |
| -------- | -------------------------------------- |
| Assessment       | Performs only assessment operation, can choose online assessment or offline assessment |
| Migration        | Performs only migration operation, no compatibility assessment for database objects, incompatibility may occur during migration |
| Validation        | Performs only validation operation                |
| Online Assessment + Migration | Performs assessment + migration operation  |
| Migration + Validation    | Performs migration + validation operation, no compatibility assessment for database objects, incompatibility may occur during migration |
| Online Assessment + Migration + Validation | Default step, full migration process operation |
| Configuration Parameters | Can be combined with the above steps for configuration parameter migration operation |

The main difference between online assessment and offline assessment is that the results of offline assessment cannot be used in subsequent migration phases. Therefore, for tasks that require migration, online assessment should be chosen.

The following are the input item descriptions when creating migration tasks:

::: tabs

== Online Assessment

|Field |Description |
| ------------ | ------------------------------------------------ |
| Task Name     | Cannot duplicate existing task names             |
| Migration Step | Supports complete migration process operation, can be chosen as needed |
| Assessment Type | Online assessment, default assessment method, it is recommended to keep the source end silent and offline |
| Source Database | Select DM                                       |
| Target Database | Select YashanDB                                 |
| Note           | Brief description of the task                    |

== Offline Assessment

|Field |Description |
| -------- | ---------------------------- |
| Task Name    | Cannot duplicate existing task names            |
| Migration Step | Only supports assessment, migration and validation not supported |
| Assessment Type | Offline assessment                             |
| Source Type   | Select DM                                      |
| Note          | Brief description of the task                   |

:::

**[ create task ]** and **[ Next: Continue Configuration ]** will check whether the Source Database connection user meets the Privileges required for the selected task steps, and any missing Privileges will prompt a popup notification.


