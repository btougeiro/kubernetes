# replicaset demo

## on this demo, I will shown a replica set creation using replicaset.yml file

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-reverse-proxy
  labels:
    app: nginx-replicaset-environment
spec:
  replicas: 4
  selector:
    matchLabels:
      env: production
  template:
    metadata:
      name: nginx
      labels:
        env: production
    spec:
      containers:
        - name: nginx
          image: nginx:1.19
```

## executing the environment

```shell script
kubectl create -f replicaset.yml
```

## author information

[Bruno Tougeiro](mailto:b.tougeiro@icloud.com)