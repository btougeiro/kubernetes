# Kubernetes StatefulSet Explained

## What is StatefulSet?

- Is a Kubernetes component used for Stateful applications

Examples of stateful applications:

- Databases (MySQL, Elasticsearch, MongoDB)
- Any application that stores data to keep track of its state

Stateless applications...

- Don't keep record of state
- Each request is completely new

Imagine a simple setup with a node js application who is connected to a mongodb database

- All requests which comes in to NodeJS doesn't depend on previous data
- NodeJS is a passthrough for data query/update
- MongoDB will update data based on previous state
- MongoDB will query data
- It'll depend on most up-to-date data/state

-> stateless application deploy using Deployment  
-> stateful applications deploy using StateFul set component

They both manage pods and you can also configure storage in the same way

So... What are the differences?

- replicating stateful applications is more difficult
- it'll require other requirements

For example: MySQL

- mysql pod handles request from a Java application
- you sacaled the java app to 3 replicas - you can do it because java is identical and interchangeable
- created in random order with random hashes
- one service that load balancer to any pod
- in other hand mysql cannot be created and deleted at the same time
- can't be randomly addressed
- replica Pods are not identical (Pod Identity)
- The Stateful application creates a sticky identity for each pod
- created from same specification, but not interchangeable
- persistent identifier across any re-scheduling

Why is this identity necessary?

- It's a concept behind the scaling database applications
- In this concept we have only 1 master as reader and writer and all the other are readers allowing multiple requests without data inconsistency
- They do not use the same physical storage and in order to achieve this we need to create a continuously synchronization of the data
- The worker must now about each change to be up-to-date
- The mechanism behind is called cluster database setup

In order to understand what happen behind the scenes:

- master -> save data to the PV /data/vol/pv-0
- worker_1 -> synchronize data from the master to the PV /data/vol/pv-1
- worker_2 -> synchronize data from the master to the PV /data/vol/pv-2

After your environment is completely running, if you need to add a new worker, the worker will get all the data from the previous worker (pod)

- worker_3 -> synchronize data from the worker_2 to the PV /data/vol/pv-3
- After the process is finished the worker will continue getting the data from the master

Each database has its own volume synchronized from the master (reader and writer)

- Data will survive, even when all pods die
- It's important to use a remote storage
- Every pod has its own identifier (deployment mysql-<hash_number> statefulset mysql-0)
- Configuring the cloning and data synchronization must to be made by you
- Make remote storage
- Stateful applications are not perfect for containerized environments
