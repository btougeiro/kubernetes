# Helm - K8S Package Manager

- What is helm?
- What are helm charts?
- How to use them?
- When to use them?
- What is Tiller?

## What is helm?

Is a package manager for Kubernetes

- To package YAML files and distribute them in public and private repositories

Imagine you created a deployment using K8S and you want to create a Elastic Stack for logging.

What would you have to do to achieve this? You would have to create all of:

- Stateful Set
- ConfigMap
- Secret
- K8S User with permissions
- Services

Create all of these files manually would be a tedious job and would take a lot of time.  
Now, once the configuration of Elastic Stack is a standard job, a lot of people would have to do the same job as you're doing.

Imagine, someone, created this yaml files once and packaged them up and made it available somewhere so that other people who needs to use the same kind of deployment, could use them in their kubernetes cluster. That bundle of files are called helm charts.

- Bundle of YAML Files
- Create your own Helm Charts with Helm
- Push them to Helm Repository
- Download and use existing ones

Examples:

- Database Apps
- Elasticsearch
- MongoDB
- MySQL

or Monitoring applications like:

- Prometheus

All those charts are available and you can use it

If you ask... Do I need some deployment? Verify Helm first before you start building it from scratch.

```shell
helm search <keywod>
```

or you can go over helm to search those charts

## Using helm (Template Engine)

Using helm, you can define a common blueprint for all the microservices and the values which are dynamic and replaced by placeholders

It would be something like this:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: {{ .Values.name }} # my-app
spec:
  containers:
  - name: {{ .Values.container.name }} # my-app-container
    image: {{ .Values.container.image }} # my-app-image
    port: {{ .Values.container.port }} # 9001
```

These values come from a file called `values.yml`. The file content would be something like this:

```yaml
name: my-app
container:
  name: my-app-container
  image: my-app-image
  port: 9001 # port example
```

.Values is an object, which is created based on the values defined.  
Values defined either via yaml files or with --set flag.

This approach is practical when you're using CI/CD. In your build you can replace the values on the fly.

## Another use case for helm features

- Same applications across different environments (development, staging, production)
- You can package them up and use the same approach for all of them (own chart)

## Helm Chart Structure

### Directory Structure

```folder_structure
mychart/ # folder name of the chart
  Chart.yml # meta info about the chart
  values.yml # values for the templates
  charts/ # folder chart and dependencies
  tempaltes/ # the actual template files
```

When you execute "helm install <chart_name>" to deploy all the files to kubernetes, the template files will be filled with the values from values.yml producing a valid k8s manifest which can be deployed in Kubernetes. Optionally you can have other files like Readme or license file.

## How values are injected into a template file?

Considering... values.yml as the default

```yaml
imageName: myapp
port: 8080
version: 1.0.0
```

You can provide an alternative values file doing the following command:

```shell
helm install --values=my-values.yml <chart_name>
```

my-values.yml will override values

```yaml
version: 2.0.0
```

the final file will be something like this:

values.yml

```yaml
imageName: myapp
port: 8080
version: 2.0.0
```

or on command line:

```shell
helm install --set version=2.0.0
```

## Third feature

- Release management (helm 2 vs helm 3)

Helm Version 2 comes in two parts. You have the helm client and the server.  
The server part is called tiller.  
Everytime when you deploy a helm chart using helm install <chartname>, helm client will send the yaml file to Tiller and Tiller will execute these requests and create componentes inside the k8s cluster. Exactly this architecture offers additional valuable feature of helm which is Release Management.

Whenever you create or change deployment tiller will store a copy of each configuration client send for future reference, thus, creating a history of chart executions.

When you execute helm upgrade <chartname>, the changes will be applied to an existing deployment instead of creating a new one. In case the upgrade goes wrong you can use helm rollback <chartname>. All of this is possible because the chart execution history and the tiller keeps a history about every deployment.

## Downside of Tiller

- Tiller has too much power inside of K8S Cluster
- It create create, update and delete components and has too much permissions
- Security Issue

In helm 3 Tiller got removed
