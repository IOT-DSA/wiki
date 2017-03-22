Dglux/Dataflow blocks naturally expect the latest value of other blocks, this makes it works perfect with qos:0 subscription. 

Some features are added into dataflow so some blocks are able to handle qos:1 subscription

### subscription block

if the qos value of subscription block is changed to any none 0 value, it starts to output a table of values instead of output a single value

### repeater

in order to handle all the input data from a qos:1 subscription block, the data of the repeater need to be directly bound from the output table of the subscription block

the mode of the repeater should be set to `stream` so it would never miss any incoming value

output of the stream repeater will be based on the speed of symbol execution, so if there are more than 1 symbol used, the output order might be different from the input data.

### script

script block can also work with qos data when its qosMode is true.

in that case, the qos data need to be directly bound to the invoke of the script. and the script should only rely on the data stored in @invoke, not other parameters created on the block.
