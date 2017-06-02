# piping

`list * | filter @unit='kwh' | subscribe $value`

 * every query method is based on the result of left pipe
 * the first method will always be based on the root node of the broker
 
# methods

## list
 * syntax: `list {path}`
 * input:  array of node
 * output: array of node
 * distributed: yes

**{path}:**
 * `*` means match any nodes
 * `?` means match one node

 exmaples
```
 list /a/b/c
 list /?/a?/?b/c
 list /a/b/c*
```

## subscribe
 * syntax: `subscribe {expression1}, {expression2}, {expression3} .....`
 * input:  array of node
 * output: array of records (with path and the record id)
 * distributed: yes

 Examples
```
 subscribe $path
 subscribe $value
 subscribe child.value
```

## filter
 * syntax: `filter {condition}`
 * input:  array of nodes or array of records
 * output: same type as input
 * distributed: yes

 examples
```
 filter $unit="F"
 filter @icon!=null
```


## invoke
invoke actions

## set
set config/attribute/value

## remove
remove config/attribute

## window
 waiting for changes and doing rollup