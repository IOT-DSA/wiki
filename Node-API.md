# Node API

The Node API is a protocol for which all implementing software must conform to. It provides a common
data set to be provided for any software that needs data from multiple devices or software. This
allows devices and software to interoperate with each other based on the data that is provided.

The protocol operates using the JSON format.

## Requests

Requests are used to retrieve data from a provider. A request always contains a method to execute
and a request ID at minimum. The method being invoked may or may not require more fields when
performing a request. Requests must be tracked by their ID until the stream is deemed closed by the
provider. The broker must track IDs for each individual client separately to avoid any ID conflict.

### Fields

Path is a special case field. If the method does not require it then it is deemed as an optional
field.

- rid (integer)
  - The request ID is how the request gets identified. All responses are asynchronous and this field
  is what is used to differentiate the requests.
  - IDs cannot be reused except in the special case to unsubscribe from data points.
- [method](methods) (string)
  - The [method](methods) is used to determine what type of request you are making.
- path (string)
  - The path is where the method should perform the operation on.
  - The path must be valid

## Responses

Responses are asynchronously returned to the corresponding request. Data is provided through
responses to the original requester. They can be streamed to provided subscription updates or provide
partial responses. Responses can (and often will) provide more fields than those listed below.

### Fields

- rid (integer)
  - The rid is the identifier to the corresponding request.
  - If the request ID is -1, then it is a subscription update.
- stream (enum string, optional)
  - The stream determines whether data will keep flowing or not.
  - If stream is omitted then it is assumed to be same value as previous stream response of same rid, default value is "initialize" if it's not shown in the first response
  - The following values are acceptable, any others will be rejected
      - initialize
          - server hasn’t finished sending existing/cached data, the values sent thus far are incomplete
      - open
          - all existing/cached data are sent, there will still be new update
          - if data provider receive a request but has nothing to send back, it still needs to send back a blank updates, with stream="open", so data consumer knows there are no cached data for the stream.
      - closed
          - the stream will no longer have any update, data consumer can close the stream
- updates (array, optional)
  - Updates are data being returned from an invoked method.
  - The method specifies what updates are returned when invoked. If omitted, there is no data being
  returned from the method. A stream can still remain open regardless of empty updates or not.
