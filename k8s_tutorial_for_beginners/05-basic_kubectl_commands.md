# Basic Kubectl commands

```shell
# get the status of the nodes
kubectl get nodes

# get the pods
kubectl get pods

# get services
kubectl get services

# create pods
# As you know, Pod is the smalled unit, but, instead of creating pods, you'll be creating deployments
# Who is an abstraction over Pods.
kubectl create deployment NAME --image=IMAGE
# the deployment has a blueprint for creating pods
# most basic configuraiton for deployment (name and image to use)
# e.g.
kubectl create deployment nginx --image=nginx:latest
```

Replicaset is a layer automatically managed by Deployments

## Layers of Abstraction

- Deployment manages a ReplicaSet
- ReplicaSet manages all the replicas of a Pod
- Pod is an abstraction of a container

### Edit a deployment

```shell
kubectl edit deployment NAME
```

## Debugging Pods
