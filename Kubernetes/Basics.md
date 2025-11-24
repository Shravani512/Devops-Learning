
# What is Kubernetes:
Kubernetes is an open-source platform that automates the deployment, scaling, and management of containerized applications.
A Kubernetes cluster is a group of machines (nodes) that work together to run containerized applications.

- A Control Plane (Master) → components that decide, manage, and control the cluster.
= Worker Nodes → machines that actually run your pods/containers.

## Kubernetes is mainly made for two core purposes:

### Container Orchestration
Automatically managing containers—deploying them, restarting them, scaling them, and ensuring they run reliably.

### Scaling & High Availability
Ensuring applications scale up/down based on load and stay available even if nodes fail.

## Kubernetes Architecture Diagram

![image alt](https://github.com/Shravani512/Devops-Learning/blob/0d3286df5d4e915614a3f0770efbf1d8cc2bd199/Images/Kubernetes%20Architecture%20Diagram.jpg)

- kubectl – CLI tool used to send commands to the Kubernetes API Server.
- API Server – The front door of Kubernetes; processes all incoming requests.
- Scheduler – Decides which worker node will run each Pod.
- Controller Manager – Ensures the actual cluster state matches the desired state.
- etcd – A distributed key-value store holding the entire cluster state.
- Worker Nodes – Machines where Pods and containers actually run.
- Kubelet – Node agent responsible for running and managing Pods as instructed by the control plane.
- CNI is the networking system in Kubernetes that gives Pods IPs and ensures communication across the whole cluster.

## Step-Wise Explanation
- kubectl is the CLI tool used to send all requests to the Kubernetes API Server.
- The API Server acts as the communication hub and receives/validates every request.
- The Scheduler decides on which worker node a new Pod should run.
- The Controller Manager ensures the cluster always matches the desired state (replicas, health, etc.).
- etcd stores the entire cluster state and configuration.
- After the control plane makes all decisions, the actual execution happens on Worker Nodes.
- On each worker node, the Kubelet runs as an agent to ensure containers are running exactly as instructed by the control plane.
- The kubelet communicates with the container runtime to create, run, and monitor containers.
- In short: kubectl → API Server → Scheduler/Controller Manager/etcd → Worker Node → Kubelet → Containers.

# Creating a Kubernetes cluster

1. **kubeadm**
A tool used to create a Kubernetes cluster manually on real or virtual machines. Mostly for learning or production on bare-metal.

2. **minikube**
A lightweight tool to run a single-node Kubernetes cluster on your local machine (good for beginners).

3. **KinD (Kubernetes in Docker)**
Runs a Kubernetes cluster inside Docker containers.
It is mostly used for:
Testing Kubernetes
CI/CD pipelines
Learning quickly without heavy setup
(That star in your image likely means recommended for practice.)

4. **AWS EKS / Azure AKS / GCP GKE**
These are managed Kubernetes services provided by cloud vendors.
You don’t create the control plane — the cloud provider manages it.

5. **Killercoda**
An online platform with free interactive Kubernetes labs running in the browser.

6. **Rancher**
A Kubernetes management platform used for deploying, scaling, and managing multiple clusters.

### KinD (Will start with this as of now)
[Shubham Londhe kinD cluster](https://github.com/LondheShubham153/kubestarter/tree/main/kind-cluster)


## Steps to create kind cluster-
- Create EC2 instance for kind cluster
- install docker
- install kind
- install kubectl
##### **Now everything in K8S is a manifest file i.e a config file (written in YML)**
- create config file(in .yml)
- create containers in cluster
-  create config.yml (kind= cluster) which have 1 control plane and 3 worker node)
-  now will create cluster using this config file command-
##### **kind create cluster --config congif.yml --name clusterNameWeWant**
-  cluster will be created according to the configuration mentioned in config.yml file
-  kubectl get nodes - control plane and worker nodes will be shown
-  kubernetes in docker kind! so now containers for control and worker node will be created (check docker ps container_id wil be shown)
-  Now if we execute the container we can see the processess in the container for worker and control node
##### **Command- docker exec -it container_id bash**
##### **ls**
##### **kubectl cluster info**
- Now if multiple clusters are there then for which cluster will the kubectl direct? So, will have to set-context for the kubectl
##### **Command- kubectl config set-context --cluster clusterName --current**
##### **kubectl get nodes**
##### **kubectl get-contexts**

## Kubernetes Cluster-

**A cluster means the full group together:**
- Control Plane
- one or more Worker Nodes

👉 This WHOLE group is called ONE Kubernetes cluster.

**KinD uses Docker containers to create nodes.**
One Docker container = one Kubernetes node
One container becomes Control Plane
Other containers become Worker Nodes

### Define Namespace
A namespace is a logical partition inside a Kubernetes cluster used to organize resources.
It helps separate projects, teams, or environments so they don’t interfere with each other.

### Define Pod-
A Pod is a small wrapper around your container that gives it an IP, storage, and runs it on a Kubernetes node. Each pod runs one single container ideally.

# Why do we create Namespaces?

1. Namespaces in Kubernetes exist for organization, separation, and control.
- Example: each one will have seperate namespace (dev namespace → Developers test new features, qa namespace → Testers run test environments, prod namespace → Real users use the application
2. To avoid name conflicts
- Two teams may create the same object name, like a Pod called nginx.(dev/nginx, prod/nginx)
3. To apply resource limits
- You can give each namespace a budget of CPU and memory.(dev → max 4 CPUs, prod → max 20 CPUs)
4. To apply access control (RBAC)
- You can restrict which team can access what.(Devs → access only dev namespace, Ops → access all namespaces, Interns → read-only access to training namespace)
5. To keep system components separate(kube-system → system components, default → your apps, kube-public → public info)

# Conceptual structure of Kubernetes structure
```
Kubernetes-Cluster/
│
├── Nodes/
│   ├── control-plane
│   ├── worker-node-1
│   ├── worker-node-2
│   └── worker-node-3
│
├── Namespaces/
│   ├── default/
│   │   ├── Pods/
│   │   ├── Deployments/
│   │   ├── Services/
│   │   └── ConfigMaps/
│   │
│   ├── kube-system/
│   │   ├── CoreDNS-pods/
│   │   ├── Kube-proxy-pods/
│   │   ├── Control-plane-components/
│   │   └── Metrics-server/
│   │
│   ├── kube-public/
│   └── your-custom-namespace/
│       ├── Pods/
│       ├── Deployments/
│       ├── Services/
│       └── Secrets/
│
└── Cluster-Wide-Objects/
    ├── StorageClasses
    ├── PersistentVolumes
    └── CRDs (Custom Resource Definitions)
```
    
## commands used-
- kind create cluster --config congif.yml --name clusterNameWeWant
- Command- docker exec -it container_id bash
- kubectl cluster info
- Command- kubectl config set-context --cluster clusterName --current
- kubectl get nodes
- kubectl get contexts
- kubectl get namespace
- kubectl apply -f namespace.yaml
- kubectl apply -f pod.yaml
- kubectl get pods
- kubectl delete pod podName -n namespaceName

Namespace.yml
```
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
```
Pod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: demo
spec:
  containers:
    - name: app-container
      image: nginx:latest
      ports:
        - containerPort: 80
```
# kubectl get namespaces

In a Kubernetes cluster, there are multiple worker nodes and multiple namespaces.
Each namespace contains many Kubernetes objects like pods, deployments, and services.
When an application needs to run, the scheduler chooses a worker node based on available resources.
On that chosen worker node, a Pod is created, and inside that Pod, containers run the application.

- All nodes connect to the same control plane.
- All nodes read the same list of namespaces.
- All nodes read the same deployments, same services, same configs, etc.
- That’s why namespaces “appear shared” — because every node talks to the same API Server which is on control plane(i.e master node)

The Scheduler assigns the Pod to a specific worker node, and the kubelet running on that worker node is responsible for creating and starting the container(s) inside that Pod.
Scheduler picks the node → Kubelet creates Pod → Pod creates container(s) → Application runs




