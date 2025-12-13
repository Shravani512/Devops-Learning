### Topics:
- HPA
- VPA
- HELM

## HPA
Horizontal Pod Autoscaler (HPA) is a Kubernetes feature that automatically increases or decreases the number of Pods in a Deployment, ReplicaSet, or
StatefulSet based on resource usage or custom metrics. HPA adjusts how many Pods are running depending on load. HPA is needed HPA as Low traffic → fewer Pods needed
High traffic → more Pods needed.(increase number of pods if the CPU usage of a pod cross perticular limit)
Without HPA:
You must manually scale Pods using command (kubectl scale deployment <deployment-name> --replicas=<number>)
Either waste resources or face performance issues
but this is not possible to do continously so it needs to be automated. wiz HPA

If using kind cluster then needs-
Metrics server as it will let know which pod is using how much CPU/Memory (kubectl top pod -n namesapceName, kubectl top node)
install metrics server, edit specs, save, restart (search commands on google)

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50

kubectl -f apply HPA.yml
kubectl get pod -n namespaceName (to check if scaled up or not)
```

you must define resource requests (and preferably limits) in your Deployment.yml.
```
resources:
  requests:
    cpu: 200m
    memory: 256Mi
  limits:
    cpu: 500m
    memory: 512Mi

kubectl -f apply deployment.yml
and then apply hpa.
```
When a Pod’s CPU usage is high, HPA increases Pods gradually, not instantly to maxReplicas.
maxReplicas is only the upper limit, not the immediate target

## VPA
Vertical Pod Autoscaler (VPA) is a Kubernetes component that automatically adjusts the CPU and memory requests and limits of Pods based on
their actual usage, so Pods always get the right amount of resources.

Pod usage → VPA → Resource calculation → Pod update

##### Steps to configure VPA
VPA is not available directly in KIND we need to clone VPA from Git repository.
cd autoscaler/
ls
vetical pod autoscaler
cd hack
./vpa-up.sh (script is run in this script all required files for vpa is installed)

```
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: nginx-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  updatePolicy:
    updateMode: Auto

if hpa is already applied then first delete it

kubectl delete -f hpa.yml
kubectl -f apply vpa.yml
kubectl get vpa -n namespacename
kubectl get top pod -n namespaceName
```
HPA (Horizontal Pod Autoscaler) increases or decreases the number of Pods when traffic or CPU load changes.
VPA (Vertical Pod Autoscaler) increases or decreases the CPU and memory given to a Pod.
HPA helps handle more users, while VPA helps give right resources to each Pod.
HPA scales sideways (more Pods), VPA scales up/down (bigger or smaller Pods).


## HELM 

Helm is a package manager for Kubernetes.
It helps you install, upgrade, and manage Kubernetes applications easily.
Instead of writing many YAML files, Helm uses charts (ready-made templates).
With one command, you can deploy complex apps like NGINX, Prometheus, or Jenkins.

Helm is a Kubernetes package manager that simplifies application deployment using reusable charts.

Steps to setup
install helm (command from google)
make it execulable
it is shell script so now run the shell script and every required things will be installed

now go to helm artifacthub.io here will get command to install any package of which name we have given and follow given steps and everything will be created.

Now for eg. i have a docker image of an application and wanted to deploy using helm
- Docker run image
- create folder for all ymls (cd helm/)
- ls
- helm create app-name
- ls (here will get forlder with app-name)
- cd app-name, ls
- templates folder will be creates
- cd templates, ls
- in app-name values.yaml folder will be present here we just need to change values and everything is done
- helm install release-name chart-name (Chart name → what to install (this chart name will be getting on artifacthub.io, Release name → name of what is installed)
- port forwarding (kubectl port-forward pod/<pod-name> 8080:80 --address=0.0.0.0)
- edit inbound rules and all will be running on the defined port!

HELM charts-
A Helm chart is a folder that contains everything needed to deploy an application on Kubernetes, it contains contains metadata, default values, and Kubernetes YAML templates required to deploy an application.
Chart name tells what to install, release name tells what to call the installed app.
