#### Topics:
- 1. Secrets and ConfigMap
 
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
