DSA permissions determine the capabilities of users and DSLinks with regard to nodes.

## Permission Groups
Each DSLink or DGLux5 user can belong to one or more user permission groups.

How permission groups are created:
 - A dsId or username automatically constitutes a permission group.
 - A DGLux5 user can create a custom user group. A custom user group is referred to by its name, preceded by the username of the user who created it, and a colon (:)—for example, `userA:userGroup`.
 - A [token](https://github.com/IOT-DSA/docs/wiki/Token-Based-Handshake) can define its default permission group.

Valid permission groups are:
 - A username
 - A dsId
 - A custom defined user group such as `userA:userGroup`
 - "default"

How permission groups are assigned:
 - A DSLink or user that has global config permission can assign a group to any DSLink or user.
 - If a token has a default permission group defined, DSLinks connected via that token have this permission group by default.

Users are added using `sys > users`.

##Permission Values
A permission value defines the capabilities of the permission group that it is assigned to.

Valid permission values are:
 - `config`
 - `write`
 - `read` 
 - `list`
 - `none`
 
## Permission Lists
A permission list is a list of pairs of `[permissionGroup, permissionValue]`. Each node can have a permission list.

If a user or a DSLink does not have permissions defined in relation to a node:
 - If the node's token has a default, then all groups that are not defined in the list have the token's default permissions.
 - Otherwise, the permission list is inherited from parent nodes.

The [root node's permission list](https://github.com/IOT-DSA/docs/wiki/permission-list) is defined in `server.json`. If no permissions are defined for the root node, then the permission system is ignored by the broker.

## Priority Level of Conflicting Permissions
If the current DSLink or user belongs to multiple groups, then the group that has the highest permission on the current node is used.

When permissions are checked, the permission check is done from root node to children nodes. If the current group has `config` permission on a node, then it also has `config` permission for all children of that node.