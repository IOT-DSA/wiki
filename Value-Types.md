## number
Number can be either a single or double. When a number type is used in the action parameter, it supports additional properties.

### Additional properties
* editor
  * color
    * Opens a color editor
  * min
    * Requires a minimum bound.
    * Note: Does not work in color editor.
  * max
    * Requires a maximum bound.
    * Note: Does not work in color editor.

### Examples
* 1
* 0.12

## string
When a string type is used in the action parameter, it supports additional properties.
### Additional properties
* editor
  * password
    * Masks the input box.
  * daterange
    * Opens a date range editor.
  * date
    * Opens a date editor.
  * textarea
    * Allows for multi-line input.
* placeholder
  * Placeholder value in a text box.

### Examples
* "hello world"
* ""

## bool
Booleans can also have labels attached to them similar to enums. The first value is always equivalent to "false" and the second value is always equivalent of "true". However, the responder *must always* send either true or false.

### Example
* bool\[disabled,enabled\]

## enum\[..\]
An enum value type can be enum\[...\] with comma separated values. The first value is always the default. The value sent over the network is the same as a string.

### Examples
* enum\[off,on,auto\]
* enum\[Sun,Mon,Tue,Wed,Thu,Fri,Sat\]

## binary
when encoded in json, byte array expressed in a string. String is prefixed with ```\u001Bbytes:```, afterwards a byte array encoded in Base64 is added.

Msgpack encoding doesn't need this special format

## map
Map object with key/value pairs. Key is always a string, and value is always of type dynamic.

### Examples
* {"name":"Rick", "email":"rick\@iot-dsa.org"}
* {"primes":[2,3,5,7,11]}

## array
An array, children values are always of type dynamic.

### Examples
* [{"name":"Rick", "email":"rick\@iot-dsa.org"}, {"name":"Dennis", "email":"dennis\@iot-dsa.org"}]
* [2,3,5,7,11]

## dynamic
The dynamic value type can be any type listed above.