#### Topics:
- Deployment/ ReplicaSet/ StatefulSets
- Services
- Ingress

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

#### Service Port = where clients enter.
#### Target Port = where your app is actually running inside the container.

![url_link](https://github.com/Shravani512/Devops-Learning/blob/1b70a6db2aea0a2cde8a9ad30284a97efde5b1b5/Images/port-matching2.png)
## nginx->pod->deployment->service->cluster->EC2

now the cluster is also made with docker so it is also in container ans we need to match its port
so will first forward the req to the cluster which is in docker container using below commands and then cluster will match port with service and then service will match port with deployment.
port matching from cluster to service and service to pod is done in service yml file so we only first need to forward client req to the cluster container port

##### Commands:
1. kubectl port-forward svc/serviceName -n namespaceName 82:82 --address=0.0.0.0
2. if above not working then sudo -E kubectl port-forward svc/serviceName -n namespaceName 82:82 --address=0.0.0.0
3. open your ec2 port 82 and now your app will run on the public ip inside a kubernetes cluster and now it is sclable!

## Ingress
Ingress is a component that gives your application a single external entry point (usually HTTP/HTTPS) and then routes traffic inside the cluster to the correct services.Without Ingress, your app needs: NodePort or LoadBalancer to be exposed. But with Ingress: You expose only one LoadBalancer, and Ingress can route traffic to many services behind it.
Path-based routing means Ingress looks at the URL path and sends the request to the correct backend service.
eg. http://mydomain.com/shop
    http://mydomain.com/user

It is present inside the cluster.
Usually created in default or ingress-nginx namespace (depends where controller is installed).
The Ingress Controller Pod runs inside the cluster.

##### Client → Ingress (LB) → Ingress Controller → Service → Pod → Application
Ingress ONLY decides where the request should go based on URL path or domain.

#### what ingress needs to work-
- ✔ An Ingress Controller (Eg. Nginx(we used in this course), Traefik, HAProxy)
- ✔ A Service backing the pod
- ✔ A LoadBalancer or NodePort (created automatically(in course) or manually)

Why we need port forwarding in ingress-
Because in local environments eg we used kind The Ingress Controller is NOT automatically exposed. Eg The Nginx ingress controller runs as a pod inside the cluster: ingress-nginx-controller : port 80 & 443 (inside cluster) But your laptop cannot directly access those ports unless: minikube exposes them OR you port-forward
http://localhost:8080
- → Reaches the Ingress Controller
- → Which routes to service → pod

- Your browser → localhost:8080 → Ingress controller → service → pod

### Ingress Controller

The Ingress Controller is the entry gateway for external traffic into a Kubernetes cluster.
While an Ingress resource only defines rules (like host-based or path-based routing), the Ingress Controller is the actual implementation that enforces those rules.

##### What the Ingress Controller Does
- Listens on ports 80/443 for external traffic.
- Reads Ingress YAML rules and converts them into routing configurations.
- Performs:
          - Host-based routing (api.example.com → api-service)
          - Path-based routing (/payments → payments-service)
- Forwards traffic to the correct Kubernetes Service.
- Handles TLS termination (HTTPS).
- Load balances traffic across pods under the Service.

  Important Notes

- It runs as Pod(s) inside your cluster.
- You need only one controller per cluster, but many Ingress resources can exist.
- Works best with ClusterIP Services (default type).
- Ingress Controller = Data Path, meaning it actually touches user traffic.
- Without an Ingress Controller, Ingress resources do nothing.

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - http:
        paths:
          - path: /shop
            pathType: Prefix
            backend:
              service:
                name: shop-service
                port:
                  number: 80

          - path: /user
            pathType: Prefix
            backend:
              service:
                name: user-service
                port:
                  number: 80
```
### Important Flow
A user sends an HTTP/HTTPS request to a public URL, which first hits the cloud entry point such as an EC2 public IP or a Load Balancer (ALB/ELB) that was provisioned by the Cloud Controller to forward traffic into the Kubernetes cluster. The request then enters a worker node where kubelet manages pods and kube-proxy applies routing rules created by Services, but the node itself does not make routing decisions. Inside the cluster, the request is picked up by the Ingress Controller (like nginx, traefik, HAProxy, or ALB Controller), which listens on ports 80/443, reads the Ingress rules, performs host/path-based routing, and forwards the traffic to the correct Service. The Service (typically ClusterIP) load-balances the request across healthy Pods and provides a stable virtual endpoint regardless of pod scaling. These resources all live inside namespaces, which logically group Services, Pods, Deployments, and Ingress resources. The Service sends the request to the appropriate application Pod created and managed through the Deployment → ReplicaSet → Pod hierarchy, where the container finally processes the request and returns the response. Meanwhile, the control plane (API Server, Scheduler, Controller Manager, etcd) is not in the data path but continuously ensures the cluster’s desired state by scheduling pods, maintaining replicas, and updating resources behind the scenes.
