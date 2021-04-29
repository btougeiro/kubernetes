# Minikube

Master and Node processes run on ONE machine. All the master processes and worker processes are running on the same server.

## Production cluster setup

- Multiple Master and Worker nodes
- Separate virtual and physical machines
- docker container pre-installed
- minikube creates a virtual box on your laptop/desktop
- node runs in that virtual box
- 1 node k8s cluster
- for testing purposes

## What is kubectl?

- Is a command line tool responsible to interact with the master node and this is the way you can create pods and manage your nodes/pods/services etc.
- Api Server enables interaction with cluster
- To create and manage your environment you do it using the API Server through a UI, API, or CLI (kubectl)
- kubectl is the most powerful of 3 clients compared to UI and API.
- Once you start dealing with the kubectl cli, you'll be able to communicate with the worker nodes and create a lot of resources
- Doesn't matter if you have a minikube cluster or a cloud cluster, kubectl will be the responsible for the communication with any kind of k8s cluster

## Installation and create minikube cluster

- <https://kubernetes.io/docs/tasks/tools/install-minikube/>
- <https://kubernetes.io/docs/tasks/tools/install-kubectl/>

Depending your operating system you may install a specific driver:

- For mac users e.g. minikube start --vm-driver=hyperkit
- <https://minikube.sigs.k8s.io/docs/drivers/>

### Kubectl CLI

... for configuring the Minikube cluster

### Minikube CLI

... for start up/deleting the cluster
