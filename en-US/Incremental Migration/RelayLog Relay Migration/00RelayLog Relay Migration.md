On the basis of TB-level data snapshots, achieving end-to-end synchronization of full and incremental data presents significant challenges:

- **Traditional Flashback Query**: Due to the expiration time of redo logs, there is a risk of flashback data becoming invalid.
- **Locking Approach**: Not acceptable in online business scenarios, as it would severely impact business operations.
- **Lock-Free Non-Flashback Approach**: Data consistency cannot be guaranteed.
- **Data Accumulation Problem**: Synchronizing TB-scale full snapshots takes a long time, potentially causing severe accumulation of incremental data at the source end.

These challenges make real-time synchronization scenarios extremely difficult.

However, adopting a lock-free, non-flashback relay log system approach, the following steps can resolve the issue of synchronizing data exceeding TB scale:

1. **Phased Full Synchronization**: Split the full-snapshot data into chunks and synchronize them in phases to the target end, with idempotent insertion on the target side.
2. **Incremental Log Collection**: Starting from the full-snapshot query start point, collect incremental logs from the source end and persist them into relay log files in the RelayLog.
3. **Relay Log Replay**: After the full-snapshot synchronization is complete, replay the relay log data from the RelayLog to the target end in an idempotent manner.
4. **Incremental Synchronization Alignment**: Both source and target ends begin incremental synchronization from the latest log file position in the RelayLog.

In scenarios involving data synchronization exceeding TB scale, direct synchronization may face risks such as network interruptions or system failures. By adopting relay log files as a buffer, the following benefits can be achieved:

- Provide resume-from-breakpoint capability for data recovery.
- Reduce direct coupling between source and target ends.
- Support idempotent processing of incremental data.
- Provide consistency guarantees at the handoff point between full and incremental synchronization.