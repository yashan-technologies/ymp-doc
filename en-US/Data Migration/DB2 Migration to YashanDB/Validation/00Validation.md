The verification phase is used to check whether the data on the source and target ends are consistent. YMP provides three modes: full verification, primary key verification, and statistical verification. However, for the DB2 source end, only statistical verification can be performed.

The consistency check is mainly used for data verification after a full migration. Users can also create a separate verification task, and the difference between the two mainly lies in the selectable object range for verification.

YMP provides a user interface guide for all configurations, monitoring, and exception handling required during the consistency check process. Users can also refer to the following documentation for guidance:

- [Validation Initialization](Validation Initialization): The initialization phase of verification tasks prepares for the verification task and mainly completes the configuration initialization of the verification task.
- [Consistency Validation](Consistency Validation): Executes the consistency check between the source and target ends based on the configuration.