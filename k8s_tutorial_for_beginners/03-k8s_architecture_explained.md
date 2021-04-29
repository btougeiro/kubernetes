# kubernetes architecture

We will talk about nodes (master and slaves) and what are the differences between them.

## Worker machine in K8S cluster

- each Node has multiple Pods on it
- 3 processes must be installed on every Node (kube proxy, kubelet, container runtime)
- Worker Nodes do the actual work
- We need on every node a container runtime running (e.g. Docker)
- The process who schedules those pods and the containers underneath is the kubelet
- The kublet has a interface between the container runtime and the machine itself
- Kublet starts the pod with a container inside
- All worker nodes must have installed kubelet and container runtime
- They comunicate between each other using services
- kube proxy makes sure the communication works between all the nodes

Little overview:

- All nodes need 3 componentes (kublet, kube proxy and container runtime)

So, how do you interact with this cluster?

How to:

- Schedule pod?
- Monitor?
- re-schedule/re-start pod?
- join a new Node?

All these managing processes are done by Master Nodes

## Master Nodes

It has 4 processes:

- Client (Api Server) - cluster gateway
- Acts as a gatekeeper for authentication

If you have some request this request is sent to the API Server -> validate request -> ... other processes ... -> POD

It's good for security because you have only 1 entrypoint into the cluster

- Scheduler

It'll check how much memory and cpu you are requesting and it'll look who is the less used worker node and will schedule the request to this node.  
The scheduler just decides on which Node new Pod should be scheduled.  

Schedule new Pod -> API Server -> Scheduler -> Where to put the Pod? -> Kublet

- Controller Manager

Detects when a Pod dies and re-schedule the new Pods as soon as possible (detects cluster state changes)

Controller Manager -> Scheduler -> Kubelet

- etcd

Key value store of a cluster state, in another words, the etcd is the cluster brain. It means every schedule, when a Pod dies, or something changes, the etcd will store it in a key value store to track all the changes. The etcd is called cluster brain because every single action created by the others items will be saved and later will be requested to the etcd. Application data is NOT stored in etcd.

## Example cluster setup

- By default in a small environment you normally have 3 worker nodes and 2 master nodes
- The master nodes normally have less cpu, memory and storage than the worker nodes who will do all the job for us

### Add new master/worker node server

- get new bare server
- install all the master/worker node processes
- add it to the cluster
