## Replace yasldr Version

To meet the needs for different versions of yasldr, YMP provides the functionality to separately replace the yasldr version. When using, you need to specify the database installation package used for replacement.
```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh replace --yasldr /data/db/yashandb-22.2.8.100-linux-x86_64.tar.gz
```

1. After replacing the yasldr version, you need to restart YMP for it to take effect. You can use `bin/ymp.sh -v` to check the version.
2. The functionality to replace the yasldr version can only be used after YMP is successfully installed.

## Replace OCI Client Version

To meet the needs for different versions of OCI, a method for replacing the OCI version is provided. The requirements for the OCI version can be found here: [Supported OCI](Installing and Uninstalling/Preparing before Installation.html#supportedDependenceVersion). Steps for replacement:

```shell
 $ cd /home/ymp/yashan-migrate-platform/
 $ sh bin/ymp.sh replace --oci instantclient-basic-linux.x64-1919000dbru.el9.zip
```

1. After replacing the OCI version, you need to restart YMP for it to take effect. You can use `bin/ymp.sh -v` to check the version.
2. The functionality to replace the yasldr version can only be used after YMP is successfully installed.
