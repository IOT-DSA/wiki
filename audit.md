audit node is located at /sys/audit

it has 2 child action nodes as well as a enabled boolean property

### config
config the audit feature, invoke this action automatically set enabled to true

example of input
```
{
  "paths": [
    "/sys"
  ],
  "auditConnect": false,
  "auditDisconnect": false,
  "auditAttributes": [],
  "ignoreActions": [
    "getHistory"
  ],
  "ignorePoints": []
}
```

* path: a list of paths to be audited
* auditConnect: audit every dsa (dslink/browser user) connection
* auditDisconnect: audit every dsa disconnection
* auditAttributes: a list of attribute names to be audited
  * by default set or remove attributes are not audited
* ignoreActions: a list of action name to be ignored
  * by default all actions in the path list are audited, except getHistory
* ignorePoints: a list of point name to be ignored
  * by default setting value of any point in the path list gets audited

### query
action to query audit history

* Source: filter dsId or username of the requester, leave blank to query all aources
* Action: invoke /  set / connect / disconnect / all
* Path: filter a sub path, leave blank to query all paths
* Date: a date range to query
