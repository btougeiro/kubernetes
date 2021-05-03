# K8S Services explained

- What is a Kubernetes Service and when we need it?
- Different Service types explained
  - ClusterIP
  - Headless
  - NodePort
  - LoadBalancer
- Differences between them and when to use which one

## What is a Kubernetes Service and when we need it?

- Pods are ephemeral
- With a service you have a solution to a robust and stable ip address
- Service also offers LoadBalancer between the Pods
- Whitin and Outside the cluster

### ClusterIP Services

- Default type
- If you do not specify a type this is the default type to be used

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: microservice-one-service
spec:
  selector:
    app: microservice-one
  ports:
  - protocol: TCP
    port: 3200
    targetPort: 3000
```

Imagine you have a microservice app deployed. Beside the microservice you have a log microservice.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice-one
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: microservice-one
    spec:
      containers:
      - name: ms-one
        image: my-private-repo/ms-one:1.0.0
        ports:
          containerPort: 3000
      - name: ms-two
        image: my-private-repo/ms-two:1.0.0
        ports:
          containerPort: 9000
```

- It mean both ports will be opened and accessible inside the Pod. A pod can also get an IP Address. If you have 3 worker nodes inside a cluster, each cluster will get a different cidr range.
- To have access inside your Pods using a service. The ingress component will receive all the requests from outside and forward to a specific service and consequently will forward to the respective Pod.
- The service will be accessible from a port
- The pods are identified using the selector key value pair. Using the same name the service will know what are the pods to manage from this service.
- The service will identify the Pods from its labels and the service will be specified the selector with the same name as we have on pod's labels
- When you create a service, kubernetes will create an endpoint with the pod's ips
- The targetPort has to match the containerPort
- If you have multiple-ports defined within your Pod, you have to name it

```yaml
ports:
- name: mongodb
  protocol: TCP
  port: 27017
  targetPort: 27017
- name: mongodb-exporter
  protocol: TCP
  port: 9216
  targetPort: 9216
```

## Headless Service (Normally used with stateful application like databases)

- Client wants to communicate with 1 specific Pod directly
- Pods want to talk directly with specific Pod
- So, not randomly selected
- Use cases: Stateful applications, like databases
- Used for synchronization between pods in case of a database

### Client needs to figure out IP Addresses of each Pod

- Option 1: API call to k8s API Server?
  - makes app too tied to K8S API
  - inefficient
- Option 2: DNS Lookup
  - DNS Lookup for Service - returns single IP address (ClusterIP)
  - Set ClustgerIP to "None" - return POD IP address instead
- To create a headless service with K8S we need to assign the ClusterIP to None. Doing this instead of assigning the ClusterIP, the IP will be assigned to the Pod
- Normally we have both services working together
- For data synchronization headless service will be used for that

## three service types attributes

- ClusterIP (This is the default type and is not needed to be specified)
- ClusterIP only accessible within cluster

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
```

- NodePort -> Creates a service which is accessible on a static port on each worker node in a cluster
- External traffic has access to fixed port on each worker node

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: microservice-one
  ports:
    - protocol: TCP
      port: 3200 # ClusterIP accessible
      targetPort: 3000
      nodePort: 30008 # range between 30000-32767 (only works inside this range)
```

- NodePort Service -> not secure because you're openning a port

- LoadBalancer

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: microservice-one
  ports:
    - protocol: TCP
      port: 3200 # ClusterIP accessible
      targetPort: 3000
      nodePort: 30008 # range between 30000-32767 (only works inside this range) only accessible by the LoadBalancer
```

- The service becomes accessible externally through cloud providers LoadBalancer

## Services

- LoadBalancer Service is an extension of NodePort service
- NodePort service is an extension of ClusterIP Service
- NodePort is not used for production environments, only used to quick tests
