# Basic Kubectl commands

```powershell
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

## Edit a deployment

```powershell
kubectl edit deployment NAME
```

## Debugging Pods

```powershell
kubectl exec -it pod/POD_NAME -- /bin/bash
```

## create a deployment using a yaml file

```powershell
kubectl create -f .\\deployment\\nginx-deployment.yml
```

## Summary

CRUD commands

- Create deployment -> kubectl create deployment NAME
- Edit deployment -> kubectl edit deployment NAME
- Delete deployment -> kubectl delete deployment NAME

Status of different K8S components

- kubectl get nodes | pod | services | replicaset | deployment

Debugging pods

- Log to console -> kubectl logs [pod name]
- Get interactive Terminal -> kubectl exec -it [pod name] -- /bin/bash
- Get info about pod -> kubectl describe pod [pod name]

Use configuration file for CRUD

- Apply a configuration file -> kubectl apply -f [file name]
- Delete with configuration file -> kubectl delete -f [file name]
