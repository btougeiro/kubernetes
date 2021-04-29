# kubernetes components

## node and pod

- inside a worker node (server) we have pods and inside pods we have containers
- usually 1 application per pod

### pod

- smallest unit of k8s
- abstratction over container
- the pod gets its own ip address, not the container but the pod
- when a pod dies another pod will be created and takes its place

### service

- permanent ip address
- lifecycle of pod and service not connected

external service -> you can use it to expose a port to your web browser  
internal service -> you can use it to expose a port internally to create communication between your pods

Using external service, as an endpoint to access your applications, is not a good idea because you'll need to deal with ips instead of names. Working with names is better than using ips. Kubernetes has a service for it and it's called "Ingress".

Pods communicate each other using a service

Service has 2 functionalities:

- permanent IP
- load balancer

### ConfigMap and Secret

- Imagine you have a database URL and usually in the built application
- To adjust the URL usually you have to re-build the application, push it to repo and pull it in your pod and restart the whole thing

For the pourpose above, k8s has a ConfigMap to deal with this problem.  
We also know the password and username can be changed. For this pourpose k8s has another component called Secret.  
It's similar to ConfigMap, but the difference is used to store secret data in base64 encoded and ConfigMap stores in plain text.

Secrets is used to store e.g. passwords, certificates, etc.

To use the secrets or config map inside your applications you can use it as environment variables or as a properties file.

## Volumes - Data Storage

As we know, if the pod dies all data will be lost.  
To deal with this problem k8s has a component called "Volumes".  
It can be: remote (outside of the k8s cluster), local, cloud-storage.

storage -> think like an external hard drive plugged in the k8s cluster

## Deployment

Blueprint for my application pods. With this, we are not creating pods anymore. We'll be creating Deployment. The deployment will be responsible to handle all the connections and communications between the services. You can also define the number of replicas you need, including scale your applications. What makes it more convenient and useful.

### Databases

- Stateful Apps: <https://cloud.google.com/kubernetes-engine/docs/how-to/stateful-apps>
- Configuration Stateful App: <https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/>
- Databases can't be replicated via Deployment
- In order to use replicas in your database, you'll need to create a mechanism who will be responsible for separate the DBs as readers and writer. Usually 1 as writer and more than 1 as reader. The purpose of this is to avoid the data corruption and data inconsistencies.

Kubernetes can offer a component called "StatefulSet". This kind of componentes is specific for applications like databases.  
e.g. elasticserach, mongodb, mysql should be created using statefulset and not deployments

StatefulSet like Deployment will be responsible to create applications and be possible to scale them up and scale then down but making sure the database reads and writes are synchronized so that no database are inconsistent.

DB are often hosted outside of K8S cluster. To manage this kind of application using K8S is quite hard.

## Summary

- Pod -> Abstraction of containers
- Service -> Communication
- Ingress -> Route traffic into cluster
- External Configuration -> ConfigMap and Secrets
- Data Persistance -> Volumes
- Pod Blueprints -> Deployments and StatefulSet (Specifically for DataBases)
