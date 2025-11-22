
#What is Kubernetes:
Kubernetes is an open-source platform that automates the deployment, scaling, and management of containerized applications.

##Kubernetes is mainly made for two core purposes:

- Container Orchestration
Automatically managing containers—deploying them, restarting them, scaling them, and ensuring they run reliably.

- Scaling & High Availability
Ensuring applications scale up/down based on load and stay available even if nodes fail.

##Step-Wise Explanation
- kubectl is the CLI tool used to send all requests to the Kubernetes API Server.
- The API Server acts as the communication hub and receives/validates every request.
- The Scheduler decides on which worker node a new Pod should run.
- The Controller Manager ensures the cluster always matches the desired state (replicas, health, etc.).
- etcd stores the entire cluster state and configuration.
- After the control plane makes all decisions, the actual execution happens on Worker Nodes.
- On each worker node, the Kubelet runs as an agent to ensure containers are running exactly as instructed by the control plane.
- The kubelet communicates with the container runtime to create, run, and monitor containers.
- In short: kubectl → API Server → Scheduler/Controller Manager/etcd → Worker Node → Kubelet → Containers.

