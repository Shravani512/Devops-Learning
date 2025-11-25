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
   We can create max 5000 kubernetes nodes and max 110 pods inside each node

#### Rolling updates in kubernetes
A Rolling Update in Kubernetes is a strategy to update an application (pods) gradually without downtime. Instead of stopping all old pods at once, Kubernetes replaces them one by one (or in small batches) with new versions. This ensures your application remains available during updates.

##### How Rolling Update Works
Suppose you have a Deployment with 3 replicas of an app:
You update the Deployment to use a new image version (e.g., nginx:1.21 → nginx:1.23).

Kubernetes starts the update:
Creates a new pod with the updated version.
Once the new pod is running and ready, it terminates one old pod.
This process repeats until all old pods are replaced.

#### Commands
- kubectl rollout status deployment/my-app
- kubectl rollout undo deployment/my-app

![link_url](https://github.com/Shravani512/Devops-Learning/blob/a551f37920c9936741de635023a06317b1294598/Images/kubernetes-flow.png)

When you run a command through kubectl, it communicates with the API Server, which is the central brain of Kubernetes. The API Server receives the request and updates the cluster state. If your command creates a Deployment or Pod, the API Server sends this information to the Scheduler, whose job is to decide which Node should run that Pod. Once scheduled, the chosen Node’s kubelet receives instructions to actually create and run the Pod. Inside the Pod, a container runs your application. To access this application, clients or other Pods do not talk to the Pod directly—instead, they talk to a Service, which provides a stable name and IP. The Service forwards incoming traffic to the correct Pod(s), even if Pod IPs keep changing. So the complete traffic flow is: Client → Service → Pod → Container, while the control flow for creating Pods is: kubectl → API Server → Scheduler → kubelet → Pod.

### Service:
A Service in Kubernetes is a logical abstraction that defines a way to access one or more pods. Pods in Kubernetes are ephemeral—they can be created and destroyed dynamically—so you need a stable way to reach them. That’s where a Service comes in.

![link_url](https://github.com/Shravani512/Devops-Learning/blob/3ade41c43bc460c9e12fbee77894f05dc939f6cf/Images/Service.png)

```
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
  namespace: default
spec:
  type: ClusterIP
  selector:
    app: my-app          # Must match pod label
  ports:
    - protocol: TCP
      port: 82           # Service entry port (clients use this)
      targetPort: 80     # Container port (app listens here)
```

Service Port = where clients enter.
Target Port = where your app is actually running inside the container.
![url_link]()
## nginx->pod->deployment->service->cluster->EC2

now the cluster is also made with docker so it is also in container ans we need to match its port
so will first forward the req to the cluster which is in docker container using below commands and then cluster will match port with service and then service will match port with deployment.
port matching from cluster to service and service to pod is done in service yml file so we only first need to forward client req to the cluster container port

##### Commands:
1. kubectl port-forward svc/serviceName -n namespaceName 82:82 --address=0.0.0.0
2. if above not working then sudo -E kubectl port-forward svc/serviceName -n namespaceName 82:82 --address=0.0.0.0
3. open your ec2 port 82 and now your app will run on the public ip inside a kubernetes cluster and now it is sclable!

 
