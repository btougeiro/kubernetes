# Complete Application Setup with Kubernetes Components

- Web App (Mongo Express)
- Mondo DB

## Overview of K8S Components

- Internal Service (MongoDB)
- Mongo Express .. to connect to DB .. to authenticate to DB

- DB User
- DB Password
- DB URL

- ConfigMap to store the URL
- Secret to store the username and password

- We'll reference both (ConfigMap and Secret) inside the deployment file using Env Var

- Create an External Service which will allow to access the service

## Request Flow

- Mongo Express -> Mongo Express External Service -> Mongo Express Pod -> Mongo DB Internal Service -> Mongo DB which will authenticate using the credentials

## Create Secret

- Check the file mongo-db-secret.yml

## Create Internal Service

- Other components and other pods can talk with this mongodb
- Check the file mongo-db-deployment.yml

## Create mongo-express

- Which database to connect? A: MongoDB Address / Internal Service
- Which credentials to authenticate? A: ME_CONFIG_MONGODB_ADMINUSERNAME ME_CONFIG_MONGODB_ADMINPASSWORD
- MongoDB Endpoint? A: ME_CONFIG_MONGODB_SERVER

## Access Mongo Express from a browser

- How to make it an External Service? Add a type after selector using the LoadBalancer type
- Create a mongo-express service to make it accessible from outside
