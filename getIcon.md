getIcon is an optional dslink api, which is a invoke node located in /sys/getIcon

```javascript
{
      "$invokable": "read",
      "$columns": [
        {"name":"Data", "type":"binary"}
      ],
      "$params": [
        {"name": "Icon", "type": "string"},
      ]
    }
  }
```

when any node returns by the list api contains a @icon attribute, broker will check if already have the cache of that icon, if not, broker will load the icon from getIcon action

the icon name should always start with a semi-unique name, and make sure same type of dslinks share same icon folder in the broker.

the icon path on the broker server will be `${serverHost}/icon/${iconPath}`

