## Enable HTTPS Service

After enabling the HTTPS service, the HTTP protocol will no longer be available. Please access YMP using the HTTPS protocol.

YMP supports the configuration of SSL certificates in JKS and PFX formats, but only one certificate can be configured at a time.

### Configure JKS Format Certificate

Step 1: Prepare a JKS format certificate.

Step 2: Modify the application.properties file and add the following configuration.

```properties
# Enable SSL switch
server.ssl.enabled=true
# Specify the storage location of the certificate, please use absolute path
server.ssl.key-store=<certificate_path>
# Specify the password for the certificate
server.ssl.key-store-password=<password>
# Specify the format of the certificate
server.ssl.key-store-type=JKS
# Optional: When there are multiple key pairs and certificate chains in the certificate file, specify the alias of the key pair and certificate to be used
server.ssl.key-alias=<key_alias>
```

Step 3: Restart the YMP service.

#### JKS Configuration Example

```properties
server.ssl.enabled=true
server.ssl.key-store=/home/yashan/mycert/domain_name.jks
server.ssl.key-store-password=123456
server.ssl.key-store-type=JKS
server.ssl.key-alias=myjks
```

### Configure PFX Format Certificate

Step 1: Prepare a PFX format certificate.

Step 2: Modify the application.properties file and add the following configuration:

```properties
# Enable SSL switch
server.ssl.enabled=true
# Specify the storage location of the certificate, please use absolute path
server.ssl.key-store=<certificate_path>
# Specify the password for the certificate
server.ssl.key-store-password=<password>
# Specify the format of the certificate
server.ssl.key-store-type=PKCS12
# Optional: When there are multiple key pairs and certificate chains in the certificate file, specify the alias of the key pair and certificate to be used
server.ssl.key-alias=<key_alias>
```

Step 3: Restart the YMP service.

#### PFX Configuration Example

```properties
   server.ssl.enabled=true
   server.ssl.key-store=/home/yashan/mycert/domain_name.pfx
   server.ssl.key-store-password=123456
   server.ssl.key-store-type=PKCS12
   server.ssl.key-alias=mypfx
```
