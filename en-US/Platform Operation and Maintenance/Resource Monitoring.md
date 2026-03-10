The resource monitoring functionality provides real-time monitoring of key system indicators in the platform's operating environment, helping users understand system resource usage.

![](./image/resource_stat.png)

|Monitoring Metric |Description |
| -------- | -------------------------------- |
| JVM Memory Usage Rate | Obtained through the memory management MXBean, calculating the used heap memory and maximize availability heap memory |
| Disk IO Usage Rate  | Obtained through the iostat command, representing the IO usage rate of the disk where YMP is deployed |
| CPU Usage Rate   | Obtained through the top command, representing the CPU usage rate of the environment where YMP is deployed |
| CPU Core Count    | The number of CPU cores in the environment where the platform is deployed |

> **Note**:
> 
> When deployed inside a container, the monitored resources are those of the host machine rather than the resources inside the container.