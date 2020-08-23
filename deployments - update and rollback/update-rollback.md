# rollout and versioning

- when we create a deployment we are creating a revision  
e.g. nginx:1.7.0 docker image would be the revision 1
- when we need to update we create another revision called revision 2  
e.g. nginx:1.7.1 docker image

all called rollout

- this help us to track our changes and to rollback if it's necessary

## deployment strategy

### Example 1 - recreate

- destroy all docker images 1.7.0
- deploy all docker images 1.7.1

possible problems --> application down

### Example 2 - rolling update (default dpeloyment strategy)

- down and up one by one

application does not go down

###  deployment command

```shell script
kubectl apply -f deployment-definition.yml
```

```shell script
# second option to update
kubectl set image deployment/deployment-name nginx=nginx:1.9.1
```

## differences -> Recreate and RollingUpdate <-

- recreate -> deployment 1 will be terminated totally and deployment 2 will be created  
creating a possible down time
- rolling update -> deployment one by one  
to see the upgrade in progress... it's possible to see 2 new replica sets created  
e.g. replica set - 1 and replica set - 2  
each container from replica set - 1 will be terminated while another one will be deployed  
on replica set - 2

## rollback

- if something goes wrong during the update process, you can rollback to a previous revision

### rollback command

```shell script
kubectl rollout undo deployment/my-deployment
```

- once you execute this command, kubernetes will rollback to a previous version the entire  
environment doing one by one
- if you execute the command `kubectl get replicasets` you'll notice the difference from a
newer version to a previous version automatically

## final consideration

- Going back to the command `kubectl run nginx --image=nginx`. This command will create a pod.  
But, behind the scenes, it will create a deployment instead of a single pod. 

## summarize commands

### create

```shell script
kubectl create -f deployment-definition.yml
```

### get

```shell script
kubectl get deployments
```

### update

```shell script
kubectl apply -f deployment-definition.yml
kubectl set image deployment/my-deployment nginx=nginx:1.9.1
kubectl replace -f deployment-definition.yml
```

### status

```shell script
kubectl rollout status deployment/my-deployment
kubectl rollout history deployment/my-deployment
```

### rollback

```shell script
kubectl rollout undo deployment/my-deployment
```

### record edit command on history

```shell script
kubectl edit deployment my-deployment --record
kubectl set image deployment my-deployment nginx=nginx:latest --record

# to see the history type
kubectl rollout history deployment my-deployment

# if something goes wrong, you can rollback to a previous state running
kubectl rollout undo deployment my-deployment
```