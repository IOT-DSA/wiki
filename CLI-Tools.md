DG Solution Builder comes with some command line tools to help with server administration tasks. The majority of these tools should be run from the root of the DG Solution Builder's installation director. Installations should ship with the appropriate shell scripting files for your platform (`.sh` for MacOS or Linux, and `.bat` for Windows).

## Users

The users tool can be used for simple administration of DG Solution Builder users when using `file` authentication. It will not work with other authentication types. There are currently 6 actions you can perform with the users tool. You can list actions by running `bin/users.sh` (or `bin\users.bat` on Windows):

### list

List current user accounts. 

Optionally specify view of `simple` which displays only usernames or detailed (default) which will display headings along with username, email (if supplied), password hash and superuser flag.

Optionally specify superusers which will only display users who have the superuser flag enabled. 

```
Usage: users list [options]
-v, --view=<name>    View
                     [simple, detailed (default)]

-s, --superusers     Show Superusers Only
```

Example:
`bin/users.sh list -v simple` may output:
```
- admin
- testUser
```

### add

Add a new user to DG Solution Builder. Username, password flags must be specified, and optionally e-mail and superuser flags may be provided.

```
Usage: users add [options]
-u, --username=<username>    Username
-e, --email=<email>          E-Mail address
-p, --password=<password>    Password
-s, --[no-]superuser         SuperUser
```

Example:
`bin/users.sh add -u newUser -e user@domain.com -p temporaryPassword -s` will create a new superuser with the username "newUser".

### remove

Deletes a user from the authorization. Any files/projects they may have created will remain on disk.

```
Usage: users remove [options]
-u, --username=<username>    Username
```

Example: `bin/users.sh remove -u newUser` will remove the with the username "newUser".

### change-password

Change the password of the specified user to the supplied password.

```
Usage: users change-password [options]
-u, --username=<username>    Username
-p, --password=<password>    Password
```

Example: `bin/users.sh change-password -u testUser -p newPassword` will change the password of "testUser" to be "newPassword". Once logged in it is still recommended the user change their password to one of their choosing.

### grant-superuser

Enable the superuser flag on the specified account.

```
Usage: users grant-superuser [options]
-u, --username=<username>    Username
```

Example: `bin/users.sh grant-superuser -u admin` will enable the superuser flag on the "admin" account.

### deny-superuser

Disable the superuser flag on the specified account.

```
Usage: users deny-superuser [options]
-u, --username=<username>    Username
```

Example: `bin/users.sh deny-superuser -u admin` will remove the superuser flag from the "admin" account.