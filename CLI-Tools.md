DG Solution Builder comes with some command line tools to help with server administration tasks. The majority of these tools should be run from the root of the DG Solution Builder's installation director. Installations should ship with the appropriate shell scripting files for your platform (`.sh` for MacOS or Linux, and `.bat` for Windows).

## Users

The users tool can be used for simple administration of DG Solution Builder users when using `file` authentication. It will not work with other authentication types. There are currently 6 actions you can perform with the users tool. You can list actions by running `bin/users.sh` (or `bin\users.bat` on Windows):

#### list

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