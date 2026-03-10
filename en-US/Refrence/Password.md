The YMP installation has the following requirements for user passwords:

* The password length must be between 8 and 64 characters and must include numbers, uppercase letters, lowercase letters, and special characters.
* The special characters do not currently support single quotes ('), double quotes ("), and ampersands (&).
* The password must not contain the string "sys".

```shell
# Change the password by entering the installation directory, using Ymppw602. as an example:
$ cd /home/ymp/yashan-migrate-platform/
$ sh bin/ymp.sh password --sys Ymppw602.
```

There are the following restrictions when changing user passwords:

* Regardless of using the internal library or the external library, the password must meet the password requirements.
* The passwords for business databases created using the external library must also meet the password requirements.