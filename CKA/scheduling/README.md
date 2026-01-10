### Manual Scheduling
*Scheduler runs in the `kube-system` namespace*  
*Assign Pods to Node by adding the property `spec.nodeName: <node_name>`*  
*Alternatively, add a binding object and send a POST request to the binding API.*

### Labels and Selectors
*use the selector flag to search by labels*  
`kubectl get pods --selector env=dev,bu=finance,tier=frontend --no-header | wc -l`  
*Add a label*  
`kubectl label node <node_name> app=myapp`

### Taints and Tolerations
*Tell the node to only accept pods with a certain toleration*  
*Ideally, a taint is automatically placed on the master node to not accept any nodes*  
*Taint the Node, add a tolerantion on the Pod*  
*Taint effects: NoSchedule, PreferNoSchedule, NoExecute*
```bash
kubectl taint nodes node-name key=value:taint-effect
# Example
kubectl taint nodes node1 app=myapp:NoSchedule
```
*Adding a toleration to a Pod add `spec.tolerations`*  
`kubectl run nginx --image=nginx --dry-run=client -o yaml`
```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
  tolerations:
    - key: "app"
      operator: "Equal"
      value: "myapp"
      effect: "NoSchedule"
```
### Node Affinity
*Its purpose is to ensure pods are hosted on particular hosts*  
*Add a `spec.affinity.nodeAffinity`*  
*Node affinity types:*
1. requiredDuringSchedulingIgnoredDuringExecution
2. preferredDuringSchedulingIgnoredDuringExecution

### Resource Limits
*Add spec.containers.resources for pod resource requests*  
*1 count of CPU = 1 AWS vCPU, 1 Azure/GCP Core*  
*Add Limits spec.containers.limits to pods to limit usage*  
*If a pod continuously hits the limits, results in OOM*  
*Requests without limits is the most ideal setup*  
*Use the LimitRange object to define default resource limits in a namespace*  
*Use resource quotas on a namespace as well*

### DaemonSets
*Ensure at least one copy of the pod runs in each node in the cluster even if a node is added later*  
*Best use case if if a monitoring or logging agent is needed on each node*  
*Pods created by daemonset are ignored by the kube-scheduler*
```bash
# use deployment to easily create a daemonset. Edit to DaemonSet
kubectl create deployment <name> -n <namespace> --image=<image> --dry-run=client -o yaml > daemonset.yml
```

### Static Pods
*kubelet can manage the node independently*  
*Configure kubelet to read manifests from a dir using `--pod-manifest-path=<path/to/dir>` option in kubelet service file or `--config=file.yml` option and specify `staticPodPath: <path/to/dir> in the file.yml`*  
*kubelet checks the dir, create and tries restarts if a pod fails*  
*delete or modify a file in the dir and kubelet automatically does the modification*  
*The pods created like this are called Static Pods.*  
*Without the kube ApiServer, use docker ps*  
*Use to deploy control plane components on the master node*  
*Static pods created by kubelet are ignored by the kube-scheduler*  
*To know the static pods, they have the nodename appended at the end i.e kube-apiserver-controlplane*  
*A static pod `ownerReferences.kind: Node`*  

### Priority Classes
*Ensure higher priority workloads are given priority*  
*The larger the number, the higher the priority*  
`kubectl get priorityclass`  
*Create a PriorityClass object then add the name value to `spec.priorityClassName`*  
*Can define globalDefault: true in PriorityClass object*  
*preemptionPolicy -> defines what will happen if pods have diff priorities*

### Logging and monitoring
```bash
kubectl top node
kubectl top pod
kubectl logs -f <pod_name> <container_name>
```