# PODs

- Kubernetes does not deploy containers directly on the worker node
- The containers are encapsulated into a kubernetes object know as PODs
- A PODs is a single instance of an application
- A PODs is the smallest object you can create in Kubernetes
- You create a number of PODs for each instance of your application
- PODs has 1 to 1 relationship with containers running your application
- To scale your environment you create more PODs and to decrease you remove some PODs
- You do not add containers to your POD to scale your application

## Multi-Container PODs

- You can have multiple containers in your PODs only if these containers are different and not running the same app
- If you have a helper container and live long side your application, you can create this new app in the same POD
- They will share the same local network and share the storage as well

## Simple Docker Containers

```shell script
docker run python-app1
docker run python-app2
docker run python-app3
docker run python-app4
docker run helper -link python-app1
docker run helper -link python-app2
docker run helper -link python-app3
docker run helper -link python-app4
```

- You will need to create all the storages, network and etc for this environment
- With PODs, kubernetes does all of this for us automatically

## How to deploy PODs

### Deprecated command

```shell script
kubectl run nginx --image nginx:latest
```

### Used command nowadays

```shell script
kubectl create deployment nginx --image nginx:latest
```

### Get PODs

```shell script
kubectl get pods
```

### Get PODs with more informations

```shell script
kubectl get pods -o wide
```

### Describe Deployment

```shell script
kubectl describe deployment nginx
```
