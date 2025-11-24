
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
- install kindctl
##### Now everything in K8S is a manifest file i.e a config file (written in YML)
- create config file(in .yml)
- create containers in cluster
-  create config.yml (kind= cluster) which have 1 control plane and 3 worker node)
-  now will create cluster using this config file command- kind create cluster --config congif.yml --name clusterNameWeWant
-  cluster will be created according to the configuration mentioned in config.yml file
-  kubectl get nodes - control plane and worker nodes will be shown
-  kubernetes in docker kind! so now containers for control and worker node will be created (check docker ps container_id wil be shown)
-  Now if we execute the container we can see the processess in the container for worker and control node
### ommand- docker exec -it container_id bash
### ls
### kubectl cluster info
- 
