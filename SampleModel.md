```javascript
{
  "defs": {
    "profile": {
      "heat": {
        "$interface": "history",
        "$type": "number",
        "$unit": "°C",
        "$settings": {
          "$max": {
            "type": "number"
          },
          "$min": {
            "type": "number",
            "min": -273.15
          },
          "$precision": {
            "type": "int",
            "min": 0,
            "writable": "config"
          },
          "$unit": {
            "type": "string"
          }
        },
        "getHistory": {
          "$is": "action/getHistory"
        }
      },
      "history": {
        "$is": "interface",
        "getHistory": {
          "$is": "action/getHistory"
        }
      },
      "action": {
        "getHistory": {
          "$is": "interface",
          "$params": [
            {
              "name": "timerange",
              "type": "timerange"
            },
            {
              "name": "interval",
              "type": "enum/historyInterval"
            },
            {
              "name": "rollup",
              "type": "enum/historyRollup"
            }
          ],
          "$columns": [
            {
              "name": "ts",
              "type": "time"
            },
            {
              "name": "value",
              "type": "object"
            }
          ]
        }
      }
    },
    "type": {
      "enum": {
        "historyRollup": {
          "$enums": [
            "avg",
            "min",
            "max",
            "sum",
            "first",
            "last",
            "count"
          ]
        },
        "historyInterval": {
          "$enums": [
            "default",
            "none",
            "oneYear",
            "threeMonths",
            "oneMonth",
            "oneWeek",
            "oneDay",
            "twelveHours",
            "sixHours",
            "fourHours",
            "threeHours",
            "twoHours",
            "oneHour",
            "thirtyMinutes",
            "twentyMinutes",
            "fifteenMinutes",
            "tenMinutes",
            "fiveMinutes",
            "oneMinute",
            "thirtySeconds",
            "fifteenSeconds",
            "tenSeconds",
            "fiveSeconds",
            "oneSecond"
          ]
        }
      }
    }
  },
  "connections": {
    "myMixIn": {
      "$permissions": [
        {
          "group": "DGLogik",
          "allow": "config"
        },
        {
          "user": "Samrg",
          "allow": "config"
        },
        {
          "group": "QA",
          "allow": "read"
        },
        {
          "group": "guest",
          "allow": "none"
        }
      ]
    },
    "heatNode1": {
      "$is": "heat",
      "$mixin": "myMixIn",
      "$min": 0,
      "$max": 100,
      "$precisson": 2
    }
  },
  "quarantine": {}
}
```