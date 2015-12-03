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


## change position
standard dsa attribute set, everything is implemented in sdk
```json
    {
      "method": "set",
      "path": "/downstream/dataflow/a/stopwatch/@x",
      "value": 281,
      "rid": 26
    },
    {
      "method": "set",
      "path": "/downstream/dataflow/a/stopwatch/@y",
      "value": 105,
      "rid": 27
    }
```


## change a binding
standard dsa invoke, responder need to store the binding value, also need to update the _@bv subscription

this is an sample request of binding stopwatch's interval from a add block's output
```json
{
  "method": "invoke",
  "path": "/downstream/dataflow/a/stopwatch/interval/_@bind",
  "params": {
    "bind": "@parent.add.output"
  },
  "rid": 117
}
```

## binding value subscription (_@bv)
`/downstream/dataflow/a/stopwatch/interval` should have a regular subscription

`/downstream/dataflow/a/stopwatch/interval/_@bv` should have another subscription with both value and binding path

standard dsa subscription request, subscribing the _@bv
```json
 {
      "method": "subscribe",
      "paths": [
        {
          "path": "/downstream/dataflow/a/stopwatch/interval/_@bv",
          "sid": 54
        }
      ],
      "rid": 127
    }
```

sample response, subscription update returns `[value, bindingPath]`
```json
{
  "rid": 0,
  "updates": [
    [
      54,
      [
        5,
        "@parent.add.output"
      ],
      "2015-12-02T18:45:49.337-08:00"
    ]
  ]
}
```

## delete a block

to delete a block, simply set the block value to null

```json
{
  "method": "set",
  "path": "/downstream/dataflow/a/add",
  "value": null,
  "rid": 222
}
```