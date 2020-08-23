# controllers and replicas

- controllers are the brain behind kubernetes

## replication controller

- if the application crashes and the pod fails it's good to setup multiple pods
- provides HA, even if you have only 1 pod, the replication controller will bring up another pod if the application fails
- create multiple pods to share the load

## replication controller and replica set

- both have the same pourpose but they are not the same
- replication controller - older technology was replaced by replica set
- replica set - is the new recommended way to setup replication

## labels and selectors

- one benefit about replica set is you can use replica set to monitor non-created pods by the replica set
- if it is not created, replica set will create all for you
- one use case... if you have hundred of pods created in your cluster, you can use the labels on your replica set
  to manage all these pods where were not created by the replica set and the labels are quite used for this
- with the labels, we can use them as filters for the replica set
- under the selector section we use the 'matchLabels' to filter these corresponding labels
- even if you have created your pods using replication controller, you will also need to create it again on template
  session on your replicaset.yml file because if one of them fail replicaset must to be able to recreate all again
  if it's needed

## scale

### change the number of replicas on your file and run the command below

```shell script
kubectl replace -f replicaset-definition.yml
```

### another approach can be used

Note: not recommended because the file won't be updated 

```shell script
kubectl scale --replicas=6 -f replicaset-definition.yml # or
kubectl scale --replicas=6 replicaset myapp-replicaset # type and service name
```