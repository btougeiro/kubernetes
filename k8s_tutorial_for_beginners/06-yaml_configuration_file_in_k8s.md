# YAML Configuration File in Kubernetes

## Overview

- The 3 parts of configuration file
- Connecting Deployments to Service to Pods
- Demo

## The 3 parts of configuration file

### Every configuration file in K8S has 3 parts

On top of the configuration file you'll have apiVersion and kind. These two are responsible to what you want to create and the apiVersion provided by Kubernetes.

#### Metadata

You can have here name and labels

#### Specification

Every kind of configuration you want to apply. The attributes of "spec" are specific to the kind.

#### Status

Automatically generated and added by Kubernetes. Kubernetes will always compare the desired state and the actual state.

Where does K8S get this status data?

- etcd holds the current status of any k8s component!
- all the cluster changes get stored in the key value store (etcd)

## Blueprint for the Pods

Inside the spec, we have the specific configuration for the pods. It's called "template". There we can define all the configurations for the Pods and it has its own metadata and spec configuration.

## Connection components (labels & selectors & ports)

The way the connection is estabilished is using labels and selectors. Metadata contains labels and Spec contains selectors.

On Metadata you can use any key-value pair

Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.16
          ports:
            - containerPort: 8080
```

This Label is matched by the selector

```yaml
selector:
  matchLabels:
    app: nginx
```

This labels are used by the Service

Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

Inside Service, the targetPort should match the containerPort.
