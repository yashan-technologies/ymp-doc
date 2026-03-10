## Uninstall YMP

````shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh stop
 $ sh bin/ymp.sh uninstall
 
 # When using uninstall functionality, the -f parameter can be used to forcefully clean the environment
 $ sh bin/ymp.sh uninstall -f
  
 # Verification
 $ ps -ef | grep yas
 ymp       20840  6322  0 10:02 pts/14   00:00:00 grep --color=auto yas
````

1. When uninstalling YMP, the default built-in database will be deleted (custom built-in database will not be affected), and the db and yashan_client folders will be emptied. If you want to change the database version, please redeploy after uninstallation.
2. The force clean functionality will use kill -9 to forcibly clean all processes started by YMP under the current user and delete all contents in the built-in library and yasldr folders. Please use with caution; it is recommended to use it under a dedicated YMP user.
3. Finally, it is also necessary to manually delete the environment variables related to YashanDB in ~/.bashrc.
4. You must execute the **stop** command to stop the migration service before running the **uninstall** command.