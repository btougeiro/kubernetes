# deployments

- download new images
- push new images
- update running containers
- rollback to a previous version
- pause
- resume

- all of these are available with deployments

- It works like layers...
    * pods (first layer)
    * replica set (second layer)
    * deployment (third layer)

## definition "deployment-definition.yml"

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: myapp
    type: frontend
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: frontend
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: frontend
```

NOTE: in fact there's no difference between replicaset and deployment  
the real difference is kubernetes will create a new object called deployment

## commands

```shell script
# create deployment
kubectl create -f deployment-definition.yml
deployment.apps/my-deployment created

# get all content
kubectl get all
NAME                                 READY   STATUS    RESTARTS   AGE
pod/my-deployment-58b8b755ff-m9wzr   1/1     Running   0          56s
pod/my-deployment-58b8b755ff-nxcvf   1/1     Running   0          56s
pod/my-deployment-58b8b755ff-tqmzh   1/1     Running   0          56s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d9h

NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-deployment   3/3     3            3           56s

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/my-deployment-58b8b755ff   3         3         3       56s

# delete deployment
kubectl delete -f deployment-definition.yml
deployment.apps "my-deployment" deleted
```

