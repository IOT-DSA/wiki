Audit feature monitors configured actions on the selected nodes and logs them to a file.

There is a dedicated `audit` node located under `/sys`.
It has two child action nodes and `enabled` boolean property.

### Config Action
This action configures the audit feature. Invoking this action automatically set `enabled` property to true.

Input example:
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

* **path** - a list of paths to be audited.
* **auditConnect** - when true audit every DSA (dslink/browser) user connection.
* **auditDisconnect** - audit every DSA disconnection if true.
* **auditAttributes** - this is a list of attribute names to be logged. By default, set or remove attributes are not audited.
* **ignoreActions** - a list of action names to be ignored. By default, all actions in the path list are audited, except `getHistory`.
* **ignorePoints** - a list of point names to ignore. By default, setting a value of any point in the path list gets logged.

### Query Action
Use this action to query audit history.

* **Source** - filter requester's dsId or username. Leave blank to query all sources.
* **Action** - invoke /  set / connect / disconnect / all
* **Path** - filter a sub path. Leave blank to query all the paths.
* **Date** - a date range to query.
