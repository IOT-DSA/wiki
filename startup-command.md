```
{dslinkAgent} --broker {brokerHost} --log [none|error|warn|info|debug]
```
### example
* jave -jar testjavalink.jar --broker localhost:8080/conn --log warn
* dart testdartlink.dart --broker https://localhost:8080/conn --log debug
* testexelink.ext --broker localhost:8080/conn --log none

### dslinkAgent 
dslinkAgent is defined in [dslink.json](dslink.json)

* **main** config defines the main executable or script
* **engines** config defines the engines that can run the main file, if engines is blank, means main file itself is executable

### broker
The url of connection end point

### log
different log level, "debug" will print all log message, and none will print no log message except critical fatal errors.

### other parameters
other parameter needed by the dslinkAgent should be defined in the configs fields in [dslink.json](dslink.json)
