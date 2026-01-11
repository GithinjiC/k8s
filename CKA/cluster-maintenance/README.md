_When a node goes offline, the master waits five minutes before considering the pods dead. This is the pod-eviction-timeout. Defaults to 5mins_  
_If pods are part of a replicaSet, it is created in other nodes_

```bash
# safely drain node of all workloads. Marks the node as cordoned(unscheduledable)
kubectl drain <node_name> --ignore-daemonsets

# mark the node as schedulable
kubectl uncordon <node_name> 

# make sure no new pods are scheduled on the node
kubectl cordon <node_name> 
```

### Cluster Upgrade - Follow the documentation

```bash
# With kubeadm
# Master node
kubeadm ugrade plan # check latest stable version
apt-get upgrade -y kubeadm=1.12.0-00
kubeadm ugrade apply v1.12.0
apt-get upgrade -y kubelet=1.12.0-00
systemctl restart kubelet

# Worker node
kubectl drain <node_name> --ignore-daemonsets
apt-get upgrade -y kubeadm=1.12.0-00
apt-get upgrade -y kubelet=1.12.0-00
kubeadm upgrade node config --kubelet-version v1.12.0
systemctl restart kubelet
kubectl uncordon <node_name>
```

_Pods are scheduled on other nodes if they belong to rd, deploy, rc, etc_  
_Best adviced to go one minor version at a time_  
_Ways to upgrade the nodes: all at once, one node at a time, add other nodes with new version then decommission older nodes(efficient for cloud)_  
_`kubectl get node` shows the kubelet version_
