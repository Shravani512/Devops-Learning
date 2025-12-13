### Topics:
- HPA
- VPA

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

