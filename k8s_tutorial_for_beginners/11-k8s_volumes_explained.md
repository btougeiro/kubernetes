# K8S Volumes Explained

- How to persist data in Kubernetes using volumes?

## Storage requirements

- Storage does not depend on the pod lifecycle
- Storage must be available on all nodes
- Storage needs to survive even if cluster crashes

Another use case for persistent storage is using a directory

## Persistent Volume

- Cluster resource is a cluster resource used to persit data
- Created via YAML file
- kind: PersistentVolume
- spec: how much storage?
- You can use a local hard drive, nfs server or cloud storage
- Kubernetes doesn't care about your local storage
- You have to decide of type of storage you want to use and manage it by yourself
- You can have multiple volumes created

## Persistent Volume YAML Example

NFS Example

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-name
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclamPolicy: Recycle
  storageClassName: slow
  mountOptions:
    - hard
    - nfsvers=4.0
  nfs:
    path: /dir/path/on/nfs/server
    server: nfs-server-ip-address
```

Google Cloud Example

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: test-volume
  labels:
    failure-domain.beta.kubernetes.io/zone: us-central-1__us-central1-b
spec:
  capacity:
    storage: 400Gi
  accessModes:
    - ReadWriteOnce
  gcePersistentDisk:
    pdNmae: my-data-disk
    fsType: ext4
```

Note: Depending on storage type, spec attributes differ

Local Storage Example

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 100Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: local-storage
  local:
    path: /mnt/disks/ssd1
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpression:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - example-node
```

Note: Persistent Volumes are NOT namespaced

- PV outside of the namespaces
- Accessible to the whole cluster

## Local vs Remote Volumes Types

- Each volume type has it's own use case
- Local volume types violate 2. and 3. requirement for data persistance
  - Being tied to 1 specific node
  - Surviving cluster crashes

For DB persistence use remote storage

## When do I need to create these volumes?

- PV are resources that need to be created before the deployment

## There're two main roles inside K8S

- K8S admin sets up and maintains the cluster
- K8S users deploy applications in cluster
- Storage resources are provisioned by Admin (PV)
- The application has to claim the Persistent Volume (PVC)

Example PVC (Persistent Volume Claim)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-name
spec:
  storageClassName: manual
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

After creating the Claim, you need to setup this configuration inside the Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: myfrontend
    image: nginx
    volumeMounts:
    - mountPath: "/var/www/html"
      name: mypd
  volumes:
  - name: mypd
    persistentVolumeClaim:
      claimName: pvc-name # it comes from the metadata name
```

- Pod requests the volume through the PV Claim
- Claim tries to find a volume in cluster
- Volume has the actual storage backend
- Claims must be in the same namespace
- The volume is mounted into the Pod

## Why so many abstratcions?

- Admin provisions storage resource (PV)
- User creates claim to PV (PVC)
- The user wants a data to be safely stored
- The user doesn't want to set up the actual storage. This is the Admin's role.

## ConfigMap and Secret

- Both of them are local volumes
- Not created via PV and PVC
- Managed by Kubernetes

## Use cases ConfigMap and Secret

- Configuration file for your pod
- Certificate file for your pod

## What we've covered so far?

- Volume is directory with some data
- These volumes are accessible by a contianer in a pod
- How made available, backend by which storage medium defined by specific volume types

## Storage Class

### To persist data inside Kubernetes

- Admins need to configure storage and create persistent volumes
- Developers can use claim PV using PVC

Now, consider a cluster with hundred of applications where the things get deployed daily and a storage is needed for these applications.  
Everytime the developers need to request the admins for storage to fit their applications and it would be a mess in a short of time.

To make this process more efficient there's a third component of kubernetes persistance called Storage Class.  
`Storage Class created or provisions Persistent Volumes dynamically when PersistentVolumeClaim claims it.`

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: storage-class-name
provisioner: kubernetes.io/aws-ebs
parameters:
  type: io1
  iopsPerGB: "10"
  fsType: ext4
```

StorageBackend is defined in the SC component

- via "provisioner" attribute
- each storage backend has own provisioner
- internal provisioner "kubernetes.io"
- external provisioner
- configure parameters for storage we want to request for PV like: type, iopsPerGB, fsType

Storage Class is another abstraction level

- abstracts underlying storage provider
- parameters for that storage

## How do we use Storage Class?

- Same as Persistent Volume it is requested or claimed by PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 100Gi
  storageClassName: storage-class-name
```

When a pod claims storage via PVC, the PVC requests storage from SC and the SC creates a PV that meets the needs of the claim.
