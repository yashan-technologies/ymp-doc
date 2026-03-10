##### 1. Redeploy without retaining any previous data

1. Execute `sh bin/ymp.sh stop && sh bin/ymp.sh uninstall -f`.
2. After executing the above steps, redeploy YMP.

##### 2. Redeploy while retaining previous data, please contact our technical support.

##### 3. Built-in library port is occupied, causing port conflict, error as shown in the figure

![](./image/port_occupancy.png)  

Please modify the port in application.properties under spring.datasource.url and in db.properties under YASDB_PORT, and ensure they are consistent.

##### 4. The ymp.sh process was forcibly stopped by *kill -9* and calling the ymp.sh script again failed, with the error as follows

![](./image/kill_process.png)

Delete the *lock* file under the *bin/* directory as prompted.