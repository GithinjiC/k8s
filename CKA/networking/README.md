_Deep dive into these networking commands_

```bash
# -n for namespaces
ip addr
ip link
route
netstat
masquerading
```

_CNI Weave_

_CNI - assigns IP addresses in k8s_  
_Check kubeapiserver config for service IP range_  

_Full domain for svc: `web-service.app.svc.cluster.local`. Format: `hostname.namespace.svc.cluster.local`_  
_DNS for pods if enabled, the '.' are replaced with '-' i.e `10-222-2-5.apps.pod.cluster.local`_

```bash
# add DNS server and add the IP entry to the pods
cat >> /etc/resolv.conf
# nameserver 10.96.0.10
```
_Kube DNS is configured by default on each cluster_  
_Deployed as CoreDNS in in the kube-system ns_
```bash
cat /etc/coredns/Corefile
```
