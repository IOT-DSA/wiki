getIcon is an optional dslink api, which is a invoke node located in /sys/getIcon

```javascript
{
      "$invokable": "read",
      "$columns": [
        {"name":"Icon", "type":"bytes"}
      ],
      "$params": [
        {"name": "Path", "type": "string"},
      ]
    }
  }
```

when any node returns by the list api contains a @icon attribute, broker will check if already have the cache of that icon, if not, broker will load the icon from getIcon action

the icon Path should always started with the dslink's standard name, and make sure same type of dslinks share same icon folder in the broker.

the icon path on the broker server will be `${serverHost}/icon/${iconPath}`

