The REST DSLink allows you to create a server that binds to a specific port. With this server, you can manipulate nodes through a REST API. The nodes are stored under a server name in the REST DSLink tree.

## API

### Fetch Node

**URL**: http://host:port/path/to/node
**Method**: GET
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
