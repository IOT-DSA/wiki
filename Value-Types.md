### number
example values:
* 1
* 0.12

### int
example values:
* 1
* -1

### uint
example values:
* 1
* 0

### string
example values:
* "hello world"
* ""

when string type is used in action parameter, it support addition properties
* editor: the editor used in GUI tool
 * can be password, daterange, date, textarea
* placeholder: the placeholder for the input box when used in GUI tool

### bool
Bools can also have labels attached to them similar to enums. The first value is always equivalent to `false` and the second value is always equivalent of `true`
* bool\[disabled,enabled\]

However the responder must always send either of these two values.
* true
* false

### enum\[..\]
a enum value type can be enum\[...\] with comma separated values, example:
* enum\[off,on,auto\]
* enum\[Sun,Mon,Tue,Wed,Thu,Fri,Sat\]

the first value in the list will be the default value

the value sent in network will look same as a string


### map
a map object with key:value pairs
key is always string and value is dynamic type
example values:
* {"name":"Rick", "email":"rick\@iot-dsa.org"}
* {"primes":[2,3,5,7,11]}

## array
a array object, children values are dynamic
example values:
* [{"name":"Rick", "email":"rick\@iot-dsa.org"}, {"name":"Dennis", "email":"dennis\@iot-dsa.org"}]
* [2,3,5,7,11]

## dynamic
a dynamic value type can have value of any above type