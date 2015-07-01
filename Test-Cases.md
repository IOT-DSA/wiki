* Responder
  * subscribe
    1. subscribe (method close should be sent to requester)
    1. update value (value should be sent)
    1. subscribe again on same path with same sid
    1. update value (value shouldn't be sent twice)
    1. ubsubscribe (method close should be sent)
    1. update value (value shouldn't be sent)
  * list
    1. list (existing data should be sent to requester)
      * children node's  $is $type $invokable $writable should be sent 
    1. add config value ( update sent)
    1. change existing config value ( update sent)
    1. add attribute and change value (update sent)
    1. remove config and remove attribute ( update sent)
    1. change $is and remove $is (no update, and the change api or remove api should close with error)
    1. add child, remove child (update sent)
  * invoke
    1. invoke action with parameter and result
    1. invoke action with no parameter and blank result
    1. invoke action and return error
    1. invoke action with column in the result
    1. do the above 4 test with delayed response, (return result after 2 seconds, nothing should be returned before that)
    1. invoke streaming action, send several rows in every 2 seconds.
  * api on node that doesn't exist
    * subscribe
       1. subscribe on a node that doesn't exist
       1. use invoke action to create that node
       1. test updating value, (update should be sent to requester)
       1. remove node with action (error should be sent to requester in the subscription stream)
       1. add node back with action
       1. test updating value
     * list 
       1. list on a node that doesn't exist
       1. use invoke action to create that node (update should be sent to requester)
       1. test updating config and children, (update should be sent)
       1. remove node with action ($disconnectedTs should be sent to requester in the list stream)
       1. add node back with action (update should be sent)
       1. test updating config and children, (update should be sent)
     * invoke and other api
       1. error should be sent to requester
    