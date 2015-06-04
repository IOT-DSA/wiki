While dsa's websocket data is transferred in json, it still allows binary data to be sent in separated binary channel. And a reference of the binary data is stored in the json


### example of a binary data

{"rid":0,"updates":[ [9,**"\u001Bbytes,10"**,"2015-06-04T21:02:52.174+00:00"] ]}

### meaning of the binary reference
* \u001B
  * escape code telling decoder this is not just a string, but a reference for binary data (or other special value) 
* bytes
  * the type of the binary data, option can be
    * bytes
    * string
  * if broker find any unknown type, the value should still be treated as a string
* 10
  * id of the message to lookup from binary channel
  * binary data must be always sent before the json message that uses it

## data structure in binary channel
each frame in binary channel contains one or more message headers and with same number of message data after them
```
[header1][header2][header3].....[data1][data2][data3]....
```

### structure of the header
```
[position(4bytes)][id(4bytes)][status(1byte)]
```
* position integer (in big endian)
  * the start position of where the data begins
  * the first data position is also the length of total data, it can be used to get a count of messages
* id (in big endian)
  * id of the message
* status
  * status of the binary channel
    * 0: closed/finished, the value of this binary data must be used or discarded when next string/json frame is received
    * 1: streaming, cache the binary and merge it until it's closed.