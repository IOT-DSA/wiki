set value on node is usually need to be serialized to hard drive.
but in certain case it's preferred not to serialize.  to achieve that, you can directly do it in dslink's logic, or read it from the dslink.json configuratio.

a config needs to be added to dslink.json like this
```json
{
  "name": "test",
.......
  "configs": {
.......
    "valuePersistenceEnabled": {
      "type": "bool",
      "value":false
    }
  }
}
```


adding this in java sdk automatically works.

in dart sdk, the serialization of node value require user code, developer can the flag to decide whether to do the serialization or not.
```dart
LinkProvider link;
// check the flag on the LinkProvider or on the SimpleNodeProvider
if (link.valuePersistenceEnabled){
  link.save();
}
```

