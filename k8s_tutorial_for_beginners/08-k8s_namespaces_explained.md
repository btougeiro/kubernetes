# K8S Namespaces Explained

- What's a namespace?
- What are the use cases?
- How namespaces work and how to use it?

## What's a namespace

- You can organize your resources in Namespaces
- Virtual Cluster inside K8S cluster

```powershell
kubectl get namespaces
```

## Kubernetes starts with four initial namespaces

- default The default namespace for objects with no other namespace
- kube-system The namespace for objects created by the Kubernetes system
- kube-public This namespace is created automatically and is readable by all users (including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster. The public aspect of this namespace is only a convention, not a requirement.
- kube-node-lease This namespace for the lease objects associated with each node which improves the performance of the node heartbeats as the cluster scales.

## create and get new namespace

```powershell
kubectl create namespace [namespace]
```

```powershell
kubectl get namespace [namespace]
```

You can also create a namespace with a configuration file

## Setting the namespace preference

You can permanently save the namespace for all subsequent kubectl commands in that context.

```shell
kubectl config set-context --current --namespace=<insert-namespace-name-here>
# Validate it
kubectl config view --minify | grep namespace:
```

## Why to use namespaces?

- Image you have only the default namespace provided by Kubernetes and you create all your resources in that default namesapce.
- If you have many components created inside the same namespace like: deployments, replicasets, services, configmaps, very soon your default namespace will be filled with a lot of resources and will be very difficult to manage this and get a good overview. Specially if we have a lof of users using the same namespace.

One simple idea to organize your environment would be something like this:

- Database -> Set all your database deployments (MySQL, MongoDB, etc)
- Monitoring -> Set all your monitoring deployments (Prometheus, etc)
- Elastic Stack -> Set all your Elastic deployments (Elasticsearch, Fluentd, etc)
- Nginx-Ingress -> Set all your ingress deployments (nginx, etc)

Officially: Should not use for smaller projects. Up to ten users.

I personally think is a good idea to group and use name spaces.

Image you have in the same cluster both environments (Quality and Production).  
You can re-use components like: Nginx-Ingress Controller and Elastic Stack to accept all the requests from both environments.

Another good example when you have Blue/Green Deployment (2 different versions of production)

- production blue
- production green

Both separated by namespaces but using the same shared resources

Access and Resources Limits on Namespaces

- Teams can only create, update and delete resources in their own namespaces.
- Each one has own, isolated environment without interfering in another namespace
- You can limit resources CPU, RAM, Storage per NAS what a team is consuming

## Characteristics of Namespaces?

- You can't access most of resources from another Namespace
- ConfigMap is an example you have to create on per Namespace
- Secrets works as the same
- There's one resource you can share is a Service
- To access a reource you need to provide the namespace e.g.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
data:
  data_url: mysql-service.database
```

- Where the database stands for the namespace name

## Components which can't be created within a Namespace

- live globally in a cluster
- you can't isolate them
- volume and node are available to the entire environment

```powershell
kubectl api-resources --namespaced=false
kubectl api-resources --namespaced=true
```

## Create components in a Namespace

- If you don't define the namespace all your resources will be created within default namespace

## Get the component created using a different namesapce

```shell
kubectl get configmap --namespace namespace-name
```

## Change active namespace

It's a little annoying type everytime --namespace when you need to get access to a resource. For this situation we have a way and we can use the kubens.

To install it on mac you can type:

```shell
brew install kubectx
```

Doing this you can access all your environment without need to type --namespace <namespace_name>
