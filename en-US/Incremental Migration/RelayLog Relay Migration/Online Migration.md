## Incremental Log Collection

When RelayLog acts as the target end, starting the migration task initiates the incremental log collection task from the source database.

> **Note**:
>
> The task must be started before full synchronization begins to avoid data loss.

### Start Task

Run the following command to start the migration task:

```bash
YDS@8100> run
```

### Monitor Synchronization Progress

Run the following command to view incremental synchronization metrics:

```bash
YDS@8100> show metrics increment
```

**Lag Metric Explanation**: Indicates that all data up to this timestamp has been synchronized to the target end. The task can be stopped safely when the lag reaches an appropriate timestamp.

### Stop Task

```bash
YDS@8100> stop
```

## Relay Log Replay
When RelayLog acts as the source end, starting the migration task immediately initiates the task of synchronizing change log data to the target database.

### Start Task

Run the following command to start the migration task:

```bash
YDS@8100> run
```

After starting, the task will automatically synchronize data from the relay log files to the target end and automatically stop upon completion.

## Obtain the Starting Point for Incremental Synchronization

Check the checkpoint file under the relay log file path in RelayLog and extract the logPosition information as the starting point for subsequent incremental synchronization.

The checkpoint file records the latest synchronization position information, serving as a critical reference point for subsequent incremental synchronization.

The checkpoint file primarily includes:

- logPosition: Extracts the latest log position information from the source end.
- File sequence number information
- File offset information

> **Note**:
>
> To avoid insufficient archive space on the source end, please clear the breakpoint resumption information in a timely manner after the incremental synchronization is completed.