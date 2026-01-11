### Manual Scheduling

_Scheduler runs in the `kube-system` namespace_  
_Assign Pods to Node by adding the property `spec.nodeName: <node_name>`_  
_Alternatively, add a binding object and send a POST request to the binding API._

### Labels and Selectors

_use the selector flag to search by labels_  
`kubectl get pods --selector env=dev,bu=finance,tier=frontend --no-header | wc -l`  
_Add a label_  
`kubectl label node <node_name> app=myapp`

### Taints and Tolerations

_Tell the node to only accept pods with a certain toleration_  
_Ideally, a taint is automatically placed on the master node to not accept any nodes_  
_Taint the Node, add a tolerantion on the Pod_  
_Taint effects: NoSchedule, PreferNoSchedule, NoExecute_

```bash
kubectl taint nodes node-name key=value:taint-effect
# Example
kubectl taint nodes node1 app=myapp:NoSchedule
```

_Adding a toleration to a Pod add `spec.tolerations`_  
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

_Its purpose is to ensure pods are hosted on particular hosts_  
_Add a `spec.affinity.nodeAffinity`_  
_Node affinity types:_

1. requiredDuringSchedulingIgnoredDuringExecution
2. preferredDuringSchedulingIgnoredDuringExecution

### Resource Limits

_Add spec.containers.resources for pod resource requests_  
_1 count of CPU = 1 AWS vCPU, 1 Azure/GCP Core_  
_Add Limits spec.containers.limits to pods to limit usage_  
_If a pod continuously hits the limits, results in OOM_  
_Requests without limits is the most ideal setup_  
_Use the LimitRange object to define default resource limits in a namespace_  
_Use resource quotas on a namespace as well_

### DaemonSets

_Ensure at least one copy of the pod runs in each node in the cluster even if a node is added later_  
_Best use case if if a monitoring or logging agent is needed on each node_  
_Pods created by daemonset are ignored by the kube-scheduler_

```bash
# use deployment to easily create a daemonset. Edit to DaemonSet
kubectl create deployment <name> -n <namespace> --image=<image> --dry-run=client -o yaml > daemonset.yml
```

### Static Pods

_kubelet can manage the node independently_  
_Configure kubelet to read manifests from a dir using `--pod-manifest-path=<path/to/dir>` option in kubelet service file or `--config=file.yml` option and specify `staticPodPath: <path/to/dir> in the file.yml`_  
_kubelet checks the dir, create and tries restarts if a pod fails_  
_delete or modify a file in the dir and kubelet automatically does the modification_  
_The pods created like this are called Static Pods._  
_Without the kube ApiServer, use docker ps_  
_Use to deploy control plane components on the master node_  
_Static pods created by kubelet are ignored by the kube-scheduler_  
_To know the static pods, they have the nodename appended at the end i.e kube-apiserver-controlplane_  
_A static pod `ownerReferences.kind: Node`_

### Priority Classes

_Ensure higher priority workloads are given priority_  
_The larger the number, the higher the priority_  
`kubectl get priorityclass`  
_Create a PriorityClass object then add the name value to `spec.priorityClassName`_  
_Can define globalDefault: true in PriorityClass object_  
_preemptionPolicy -> defines what will happen if pods have diff priorities_

### Logging and monitoring

```bash
kubectl top node
kubectl top pod
kubectl logs -f <pod_name> <container_name>
```
