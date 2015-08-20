The REST DSLink allows you to create a server that binds to a specific port. With this server, you can manipulate nodes through a REST API. The nodes are stored under a server name in the REST DSLink tree.

## API

**Node URL**: `http://host:port/path/to/node`

### Fetch Node

**Method**: GET<br/>
**Example Response**:

```json
{
  "?name": "hello",
  "?path": "/data/hello",
  "$is": "node",
  "$type": "string",
  "?value": "Hello World",
  "?value_timestamp": "2015-08-19T21:50:40.624-04:00"
}
```

## Create/Update Node

**URL**: http://host:port/path/to/node<br/>
**Method**: PUT<br/>
**Example Request**:
```json
{
  "$type": "number",
  "@unit": "%",
  "?value": 30
}
```

**Example Response**:
```json
{
  "?name": "percentage",
  "?path": "/data/percentage",
  "$is": "node",
  "$type": "number",
  "@unit": "%",
  "?value": 30
}
```