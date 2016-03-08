## permission group
 1. each dslink or dglux user may have one or mutlipe user permission group
 1. dslink's dsId and username itself are also a permission group
 1. dglux user can define his custom user group, his permission group will be prefixed by his username and a `:`, like `userA:mygroup`
 
## permission list
 1. each node can have a permission list, which defines pairs of [permissionGroup, permissionValue]
 1. valid permission values are `config` `write` `read` `list` `none`
 1. permission group can be a permission, a username, a dsId or 'default'
 1. the 'default' group is optional in the list. if defined, it matchs all the other groups that's not defined in the list
 1. if permission list is not defined or doesn't define the current user/dslink's group, the permission list is inherited from parent nodes
 1. [root node's permission list](https://github.com/IOT-DSA/docs/wiki/permission-list) is defined in server.json, if it's not defined, then permission system is ignored by broker

## permission check
 1. the permission check is done from root node to children nodes
 1. if current group has `config` permission on a node, all children nodes permission lists are ignored
 1. if dslink/user has multiple groups, it uses the group that has the highest permission on current node

## assign group
 1. dslink/user that has global config permission can assign group to any dslink/user
 1. a [token](https://github.com/IOT-DSA/docs/wiki/Token-Based-Handshake)can define its default permission group, and dslink that's connected with this token will have this permission group by default.