# microservices application

## sample application - voting application

voting-app > in-memory db > worker > db > result-app

### creating the application on docker
 
 ```shell script
docker run -d --name=redis redis
docker run -d --name=db postgres:9.4
docker run -d --name=vote -p 5000:80 voting-app
docker run -d --name=result -p 5001:80 result-app
docker run -d --name=worker worker
```
- it won't work until you link these containers together

 ```shell script
docker run -d --name=redis redis
docker run -d --name=db postgres:9.4
docker run -d --name=vote -p 5000:80 --link redis:redis voting-app # --link hostname:service_name_looked_By_container
docker run -d --name=result -p 5001:80 --link db:db result-app
docker run -d --name=worker --link redis:redis --link db:db worker
```

### creating the application on kubernetes

#### goals
- deploy containers
- enable connectivity
- external access

#### steps
- deploy pods
- Create Services (ClusterIP)
  - redis (image=redis)
  - db (image=postgresql)
- Create Services (NodePort)
  - voting-app (image=kodekloud/examplevotingapp_vote:v1)
  - result-app (image=kodekloud/examplevotingapp_result:v1)
- Worker (image=kodekloud/examplevotingapp_worker:v1)

pod voting-app port 80    
pod redis port 6379  
pod worker  
pod postgresql port 5432  
pod result-app port 80

```shell script
# commands
kubectl create --save-config -f <all_files>
kubectl delete -f <all_files>
kubectl apply -f <all_files> # or
kubectl scale deployment <deployment-name> --replicas=<number_of_replicas>
```

#### next step
- convert to a deployment