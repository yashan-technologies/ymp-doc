DMC supports online migration from YashanDB to Oracle database, including full data synchronization and incremental data synchronization.

It is recommended to perform full data synchronization when there is less business activity on the source end, to reduce the impact on online business and achieve a better user experience.

Please read the migration guidance in the order listed in this manual before starting the migration task to avoid unexpected errors.



## Incremental Sync Task Backpressure

DMC achieves incremental data capture for YashanDB through the YStream service of YashanDB. During operation, the YStream server will monitor the backpressure situation of DMC's tasks. The following situations will determine a migration task failure:

- If a single LCR is not consumed within 3600 seconds.

- If the number of non-DDL type LCRs in the source.options.connectorParameter.queueSize field of the task configuration file has not been consumed within 3600 seconds, and exactly one DDL arrives, the migration task will fail.
- Serious data backpressure exceeds 1 day.

It is recommended to assess the consumption capability of the target connector before running the incremental sync task, and try to perform DDL operations during off-peak business hours. If the consumption capability is poor, consider reducing the source.options.connectorParameter.queueSize. If you want to achieve higher DML throughput, you can appropriately increase this parameter.

The evaluation method is: perform stress testing on an incremental task using the DMC link task, and frequently use the command `show metrics increment` on the client to check the growth rate of receiveLcrCount to confirm whether the above situations exist and make appropriate parameter adjustments.

If you wish to adjust this parameter while the incremental sync task is running, you need to pause the task, set the parameters, and then continue the synchronization using DMC's checkpoint resume capability.

## YashanDB Node Switching

For ease of understanding, let's assume an incremental task named switch_task is running with the default configuration file `conf/task_config.yml`, and you want to switch nodes.

The current task on the DMC source end is connected to YashanDB located at `192.168.1.2:1688`, and you want to switch to YashanDB at `192.168.1.3:1688`.

```yml
taskName: switch_task

source:
  type: YashanDB
  dataSource:
    hostname:
      - host: 192.168.1.2
        port: 1688
    username: SYS
    password: HwfydolzxogxdRxTEmRQyrEq54NceA==
```

**Switching while the task is running normally**:

1. First, execute the `stop` command in the DMC client to terminate the task.

   ```shell
   YDS@8100> stop
   ```

2. Log in to the source end's old node database, using the task name corresponding to the configuration file to stop YStream.

   ```shell
   # Log in to the database
   $ yasql SYS/******@192.168.1.2:1688
   # Stop YStream
   SQL> EXEC DBMS_YSTREAM_ADM.STOP('switch_task');
   ```

3. Log in to the source end's new node database and start YStream.

   ```shell
   # Log in to the database
   $ yasql SYS//******@@192.168.1.3:1688
   # Start YStream
   SQL> EXEC DBMS_YSTREAM_ADM.START('switch_task');
   ```

4. Modify the dataSource in the DMC configuration file.

   ```yml
   taskName: switch_task
   
   source:
     type: YashanDB
     dataSource:
       hostname:
         - host: 192.168.1.3
           port: 1688
       username: SYS
       password: HwfydolzxogxdRxTEmRQyrEq54NceA==
   ```

5. Resume the task.

   ```shell
   YDS@8100> resume
   ```

**Switching when the source database (`192.168.1.2:1688`) crashes**:

1. Log in to the source end's new node database, using the task name corresponding to the configuration file to restart YStream.

   ```shell
   # Log in to the database
   $ yasql SYS//******@@192.168.1.3:1688
   # Stop YStream
   SQL> EXEC DBMS_YSTREAM_ADM.STOP('switch_task');
   SQL> EXEC DBMS_YSTREAM_ADM.START('switch_task');
   ```

2. Modify the dataSource in the DMC configuration file.

   ```yml
   taskName: switch_task
   
   source:
     type: YashanDB
     dataSource:
       hostname:
         - host: 192.168.1.3
           port: 1688
       username: SYS
       password: HwfydolzxogxdRxTEmRQyrEq54NceA==
   ```

3. Resume the task.

   ```shell
   YDS@8100> resume
   ```

## Creating Heartbeat Auxiliary Table on Source End by DMC

After enabling the heartbeat functionality (with `options.heartbeatEnabled` set to true), DMC will create a heartbeat auxiliary table on the source end, named $YDS_HEARTBEAT_INSTANCE_NAME.

If you later need to delete the heartbeat auxiliary table, the user must delete it manually.


