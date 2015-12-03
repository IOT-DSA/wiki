## create dataflow 

this is a optional normal dsa action to create a dataflow, not required if there is another way to create dataflow

#### sample request
```json
{
  "method": "invoke",
  "path": "/downstream/dataflow/createDataflow",
  "params": {
    "name": "a"
  },
  "rid": 71
}
```

## list of a dataflow

in the list result of a dataflow object, $is must be `dglogik/df/dataflow`

#### sample list response
should also 
```json
{
  "rid": 72,
  "stream": "open",
  "updates": [
    [
      "$is",
      "dglogik/df/dataflow"
    ],
    [
      "$dgType",
      "dataflow"
    ],
    [
      "$dgId",
      21
    ],
    [
      "stopwatch",
      {
        "$is": "dglogik/df/stopwatch",
        "$dgType": "stopwatch",
        "$dgId": 10
      }
    ]
  ]
}
```

## create block, create 

#### sample json
add a block with name 'stopwatch' and @type 'stopwatch'
``` json
{
  "method": "invoke",
  "path": "/downstream/dataflow/a/_@addObject",
  "params": {
    "name": "stopwatch",
    "data": {
      "@type": "stopwatch",
      "interval": 0.5,
      "step": 1
    }
  },
  "rid": 85
}
```


## list a block
a sample response of the list api on `/downstream/dataflow/a/stopwatch`
```javascript
{
  "responses": [
    {
      "rid": 33,
      "stream": "open",
      "updates": [
        [
          "$is",
          "dglogik/df/stopwatch"
        ],
        [
          "$writable",
          "write"
        ],
        [
          "$dgType",  // the block type
          "stopwatch"
        ],
        [
          "$dgId",  // a unique id for each instance
          10
        ],
        [
          "@ps",    // standard attribute, list of properties that's shown in the block UI
          [
            "enabled",
            "interval",
            "step",
            "output"
          ]
        ],
        [
          "@x",
          127
        ],
        [
          "@y",
          86
        ],
        [
          "enabled",    // block property point
          {
            "$is": "node",
            "$type": "bool",
            "$writable": "write"
          }
        ],
        [
          "interval", // block property point
          {
            "$is": "node",
            "$type": "number",
            "$writable": "write"
          }
        ],
        [
          "output", // block property point
          {
            "$is": "node",
            "$type": "number",
            "$writable": "write"
          }
        ]
      ]
    }
  ],
  "msg": 92
}
```