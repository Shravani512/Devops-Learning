#### Topics:
- 1. Secrets and ConfigMap
- 2. Persistence Volume
 
##### Check errors in kubernetes
- kubectl describe pod/pod_id -n namespaceName
- kubectl log pod/pod_id -n namespaceName

## Secrets:
Secrets are Kubernetes objects used to store sensitive data like passwords, tokens, certificates, and keys in a safer way than putting them directly in YAML files or environment variables.
How Secrets Work Internally

## ConfigMap
A ConfigMap is a Kubernetes object used to store non-sensitive configuration data in key–value format. Eg- application properties, URLs, feature flags, environment-specific configurations, file-based configs (JSON, YAML, .env, etc.)
ConfigMap = non-sensitive configuration
Secret = sensitive, confidential configuration

![link](https://github.com/Shravani512/Devops-Learning/blob/2879f3c1ea3d4dadd6229a6b2402e390b7d9671b/Images/Secrets.png)

### You create a Secret (base64-encoded key-value data).
- Kubernetes stores it in etcd, encrypted if encryption-at-rest is enabled.
- Pods reference the Secret via:
- Environment variables, or
- Volumes mounted into the container
- The kubelet fetches the Secret only when needed and injects it into the pod.
- Role-Based Access Control (RBAC) controls who can read or modify Secrets.

Example:
You have a web app pod that needs:
DB username
DB password
Instead of hardcoding inside Deployment YAML, you store them in a Secret → pod reads them safely at runtime.
## Secret
```
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzczEyMw==
```
### Use Secret in pod-
1. As environment variables
   The Secret is injected as environment variables inside the container.
 ```
   env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: username
```

2. As volume mount
   The Secret is mounted as files inside the container.
```
volumeMounts:
  - name: secret-volume
    mountPath: /etc/secret
    readOnly: true
volumes:
  - name: secret-volume
    secret:
      secretName: db-secret
```
### Sample Deployment.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: myimage:latest

          # 1) ENV variables from Secret
          env:
            - name: DB_USER
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: username
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: password

          # 2) Volume mount to inject secret as files
          volumeMounts:
            - name: secret-volume
              mountPath: /etc/secret
              readOnly: true

        # 3) Volumes section at pod level (not inside container)
      volumes:
        - name: secret-volume
          secret:
            secretName: db-secret
```
# App (example in Node.js) reading the env
# (this is where the name is used) 
process.env.DB_PASSWORD
In application code, developer define an environment variable name like DB_PASSWORD. In the Deployment YAML, the same name is used under env: and map it using valueFrom.secretKeyRef. Kubernetes then fetches the actual value from the Secret defined in secret.yml, and that Secret is stored securely inside etcd.
Command - kubectl -f apply secret.yml

### ConfigMap
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MODE: "production"
  LOG_LEVEL: "debug"
  SERVICE_URL: "http://my-service.default.svc.cluster.local"
```
In deployment.yml everthing is same as secret difference is instead of secretKeyRef we have configMapKeyRef

# Persistent Volume Storage
A Persistent Volume is a cluster-level abstraction in Kubernetes that provides persistent storage independent of Pod lifecycle and is consumed via Persistent Volume Claims.

In Kubernetes, Persistent Volume (PV) is used to store data permanently, even if a Pod dies, restarts, or is recreated.
##### Why do we need Persistent Volume?
By default, Pod storage is temporary If a Pod is deleted → data is lost, this is a problem for: Databases (MySQL, PostgreSQL, Logs, File uploads, Application state Persistent Volume solves this problem.
Persistent Volume (PV) is storage in Kubernetes that keeps data safe even if a Pod is deleted or restarted.
It is separate from the Pod and represents real storage like a disk or cloud volume.
Pods use it indirectly through a Persistent Volume Claim (PVC).

Pod → PVC → PV → Actual Storage

Pod uses PVC
PVC is bound to PV
PV connects to physical storage

Access modes:
ReadWriteOnce (RWO) → Mounted by one node
ReadOnlyMany (ROX) → Read-only by many nodes
ReadWriteMany (RWX) → Read-write by many nodes

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-demo
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data (node Path where the data of pod will be stored)

We will not define namespace because PV is created which is cluster based.
kubectl -f apply persistentVolume.yml
```

##### Persistent volume claim
PersistentVolumeClaim (PVC) is a namespace-level request for storage in Kubernetes.
It asks Kubernetes for storage, and Kubernetes binds it to a PersistentVolume (PV).
Pods in the same namespace then use this storage through the PVC.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-demo
  namespace: default
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

kubectl apply -f pvc.yml
kubectl get pvc -n namespacename (status is pending) because we need to attach it to a pod
```

Here in pvc we will define namespace because claim is for namespace so 1 claim for 1 namespace
kubectl -f apply PersistentVolumeClaim.yml

```
Mount to pod in deployment.yml
 spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: storage
          mountPath: /var/lib/mysql (path of main volume from where PV is taken)
      volumes:
      - name: storage
        persistentVolumeClaim:
          claimName: pvc-demo
kubectl -f apply deploment.yml
kubectl get pvc -n namespacename (status is bound) because now pv is bound to a pod.
```
understand the analogy-
in pv.yml we gave path of node where the data of pod will be stored, then in pvc we claim pv to perticular namespace and in deployment.yml we attach to pod(all pods will be attached automatically) and mentioned volume mount which is path of main volume from where we create pv.

node path(where pod data will be stored) <- pv.yml -> pvc.yml -> namespace -> main volume path

Now check if data is really getting stored in storage created-
kubectl get pods -n namespaceName
kubectl describe pod pod_id(any) (here we can see to which node the volume is attached)
so now get id of that node command (docker ps) because node is a container in kind so command is docker ps
Now enter in that node command (docker exec -it node_id)
now go to the node path mentioned in the pv 
cd mnt, cd data, ls ----- boom data of pod will be stored at this location of the node 
so now as the data is stored in node of the perticular namespace for data will not loss even if a Pod dies, restarts, or is recreated.

