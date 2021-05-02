# Kubernetes Ingress Explained

- my-app (pod) <- my-app (service) <- my-app (ingress) <- web browser <http://my-app.com>

Example:

You need to map the domain name to Node's IP Address, which is the entrypoint.  
e.g. myapp.com -> entrypoint's ip address (node) or a service outside the cluster

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  rules:
  - host: myapp.com # it has to be valid
    http: # step: incoming request gets forwarded to internal service
      paths:
      - backend: # target to be redirected
          serviceName: myapp-internal-service
          servicePort: 8080
```

The user will type myapp.com on his/her web browser, and the ingress service will automatically forward to the internal service so on and so forth.

## How to configure Ingress in the cluster?

- You need an implementation for Ingress
- Which is Ingress Controller
- evaluates and processes ingress rules

## What is Ingress Controller?

- Evaluates all the rules
- Manages redirections
- Entrypoint to cluster
- Many third-party implementations

One example of ingress implementations -> K8S Nginx Ingress Controller

- First of all you need to consider the environment on which your cluster runs
- If you are using a cloud service provider: aws, linode, gcp they normally have out-of-the-box k8s solutions and/or own virtualized load balancer

You'll have something like this:

cloud load balancer -> Ingress Controller -> Service -> K8S Environment

What are the advantages of this?

- You don't have to implement load balancer by yourself and you have a ready environment provided by the cloud platform.
- If you are using a bare metal environment you'll need to configure some kind of entrypoint either inside of cluster or outside as separate server
- Using a bare metal -> http request managed by a reverse proxy -> ingress controller -> internal services

## Test inside Minikube

- Install Ingress Controller in Minikube

```shell
minikube addons enable ingress
```

This command will automatically start the K8S Nginx Implementation of Ingress Controller

To check if the Ingress Controller is working properly you can type the following command:

```shell
kubectl get pod -n kube-system
```

Look for a pod called: nginx-ingress-controller-*

## looking into the configuration

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  namespace: nginx-ingress-controller
spec:
  defaultBackend:
    service:
      name: nginx-service
      port:
        number: 8080
  rules:
  - host: nginx-ingress.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 8080
```

The example above is good to demonstrate how to configure a lot of applications inside the same environment  
For example, if you have more than 1 app, you can configure it using the paths.  
For each path, you can create a completely independent service provider with a specific pod  

```yaml
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /analytics
        backend:
          service:
            name: analytics-service
            port:
              number: 3000
      - path: /shopping
        backend:
          service:
            name: shopping-service
            port:
              number: 4000
```

- If you access the host <http://myapp.com/analytics> the service analytics-service will be responsible to attend the request
- If you access the host <http://myapp.com/shopping> the service shopping-service will be responsible to attend the request

## Multiple sub-domains

```yaml
spec:
  rules:
  - host: analytics.app.com
    http:
      paths:
      - path: /
        backend:
          service:
            name: analytics-service
            port:
              number: 3000
  - host: shopping.app.com
    http:
      paths:
      - path: /
        backend:
          service:
            name: shopping-service
            port:
              number: 4000
```

## Configuring TLS Certificate

- First you'll need to create a Secret like the example below

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: nginx-tls
  namespace: nginx-ingress-controller
data:
  tls.crt: base64 encoded cert # echo -n <certificate_content> | base64
  tls.key: base64 encoded key # echo -n <key_content> | base64
type: kubernetes.io/tls
```

- After that you can create the ingress rule

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-nginx-ingress
  namespace: nginx-ingress-controller
spec:
  tls:
  - hosts:
    - nginx-ingress.local
    secretName: nginx-tls
  rules:
  - host: nginx-ingress.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 8080
```
