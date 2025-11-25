#### Topics:
- Deployment/ ReplicaSet/ StatefulSets
- Services
- Ingress
- Persistent Volumes and Persistent Volume claims
- Secrets and ConfigMaps

## What is a Deployment in Kubernetes?

A Deployment is a Kubernetes resource that manages a set of identical pods. It ensures that a specified number of pod replicas are always running and can update them safely.Think of a Deployment as a manager for your pods: it keeps them running, updates them when needed, and can scale them up or down automatically.

### Key points:
- Declarative: You define what you want (e.g., 3 replicas of a pod), not how to achieve it.
- Self-healing: If a pod crashes, the Deployment automatically creates a new one to maintain the desired state.
- Rolling updates: You can update the application without downtime. Kubernetes updates pods gradually.
- Deployment: Manages pods, ensures desired state, allows updates without downtime.
- Scaling: Achieved manually or automatically via HPA; Deployment ensures the correct number of pods run.

### Deployment.yml:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  label:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: nginx:latest
        ports:
        - containerPort: 80
```
Spec here is our pod.yml
to create replicas of pod these pod is matched with the lable of pod mentioned in matchLables

#### Commands:
1. kubectl apply -f deployment.yml --dry-run=client
2. kubectl apply -f deployment.yml
3. kubectl get deployment -n namespaceName (deployment name will be listed)
4. kubectl get pods -n namespaceName (all pods with status running.. will be shown)
Now if we delete any pod new pod will be automatically created and desired number of pods will be maintained eg we mentioned 5 in replicas and deleted 1 then immediately automatically 1 new pod will be created
5. kubectl detele pod deploymentName pod_id -n namespaceName
We can scale up and scale down the number of pods in deployment we want
6. kubectl scale deployment deploymentName -n namespaceName --replicas=10

#### Rolling updates in kubernetes
