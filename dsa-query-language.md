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
 list a/b/c
 list ?/a?/?b/c
 list a/b/c*
```

## subscribe
 * syntax: `subscribe {expression1}, {expression2}, {expression3} .....`
 * input:  array of node
 * output: array of records (with path and the record id)
 * distributed: yes

 exmaples
```
 subscribe $path
 subscribe $value
 subscribe child.value
```

## filter
 * syntax: `filter {condition}`
 * input:  array of node or array of records
 * output: same type as input
 * distributed: yes

 exmaples
```
 filter $unit="true"
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