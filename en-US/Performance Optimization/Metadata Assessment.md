### Assessment Database Performance Optimization

The YMP metadata assessment will use the specified assessment database to execute the retrieved DDL. Performance assessment relies on the parallel execution performance of the assessment database, and database configuration optimization refers to the YashanDB Performance Optimization Manual.

### YMP Metadata Assessment Tuning Configuration Parameters

|Parameter Name |Default Value |Parameter Description |
| ------------------------- | ------ | ------------------------------------------------------------ |
| assessment.ddlCount       | 20            | The number of DDLs retrieved per session for the assessment task. If Oracle performance is poor, this value needs to be reduced. |
| assessment.maxThreadCount | 20            | The maximum number of sessions that the assessment task can have simultaneously. If Oracle performance is poor, this value needs to be reduced. |