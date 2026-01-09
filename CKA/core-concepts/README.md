### Ref Commands
```bash
kubectl api-resources
kubectl explain -h
kubectl explain pods --recursive
``` 

### Pods

```bash
kubectl apply -f simple-pod.yml
kubectl get pods -o wide
kubectl describe pod <pod-name>
kubectl run redis --image=redis -n=dev
kubectl run custom-nginx --image=nginx --port=8080
kubectl run redis --image=redis --dry-run=client -o yaml > redis.yaml # generate a yaml file and edit
```
*Create a pod called httpd, a service of type ClusterIp by the same name and target-port=80*   
```bash
kubectl run https --image=httpd:alpine --port=80 --expose=true
```

### Replication Controller

```bash
kubectl create -f rc-definition.yml
kubectl get replicationcontrollers
```
*Lookup difference between repliaction controller and replicaset*

### ReplicaSets
```bash
kubectl explain replicaset
kubectl apply/create -f replicaset-def.yml
kubectl get replicasets.apps -> or kubectl get rs
kubectl edit rs <rs-name>
kubectl describe replicasets.apps myapp-replicaset
kubectl delete replicasets.apps <replicaset-name>
kubectl replace -f replicaset-def.yml
kubectl scale --replicas=6 -f replicaset-def.yml
```

### Deployments
*Deployments create replicasets*

```bash
kubectl create -f first-deployment.yml
kubectl create deployment nginx --image=nginx
kubectl get deployments
kubectl get rs
kubectl get pods
kubectl expose deployment nginx --port=80
```

### Services
```bash
kubectl create -f simple-service.yml
kubectl get services or kubectl get svc
curl http://192.168.1.2:30008 # service node range 30000-32767
db-service.dev.svc.cluster.local # name to access a service(db-service) in another namespace(dev)
```

*Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379*
```bash
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```

### Namespaces
```bash
kubectl create -f namespace.yml
kubectl create namespace dev
kubectl config set-context $(kubectl config current-context) --namespace=dev # use to switch namespaces
```

*To create a resource quota use the resource-quota.yml*
```bash
kubectl create -f resource-quota.yml
```
