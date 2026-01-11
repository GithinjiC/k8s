_CertificateSigningRequest object_

```bash
cat cos.csr | base64 -w 0`
kubectl create -f cos.yml
kubectl get csr
kubectl certificate approve cos

# To deny and delete a csr, e.g req for groups:master
kubectl certficate deny agent-smith
kubectl delete csr agent-smith
```

_kubeConfig_

```bash
cat $HOME/.kube/config
kubectl config view
kubectl config use-context <context_name> # change current context
kubectl config use-context <context_name> --kubeconfig /path/to/new/config
kubectl config -h
```

_Role-Based Access Control(RBAC)_  
_Roles and role bindings are namespaced_

```bash
# Create a Role Object
kubectl create -f role.yml

# Create RoleBinding object
kubectl create -f role-binding.yml

kubectl get roles --no-headers | wc -l # for count of roles
kubectl get rolebindings
kubectl describe role developer
kubectl describe rolebindings <name>

# Checking Access
kubectl auth can-i create deployments
kubectl auth can-i delete nodes
# check if dev-user can create deployments
kubectl auth can-i create deployments --as dev-user --namespace <namespace>
kubectl get pods --as dev-user
```

_Cluster Scoped resources: nodes, pv, namespaces, csr_  
_Use ClusterRole and ClusterRoleBinding objects_  
_Clutser roles and bindings are not namespaced_

```bash
kubectl api-resources --namespaced=true # namespaced resources
kubectl api-resources --namespaced=false # cluster scoped resources
```

_ServiceAccounts -> used to connect to 3rd party such i.e Prometheus_  
_Tokens are created for service accounts to prove identity_  
_For deployments, add the serviceAccountName under the pod spec def_

_Image Security_  
_create a secret of type docker-registry and add in a name entry in `spec.container.imagePullSecrets` on the Pod manifest_

_Add `spec.securityContext` to set userId(`runAsUser`) on pod. Can be added under `spec.containers` to set on container level and add `capabilites`_

_Create a NetworkPolicy object to define pods' ingress and egress traffic_

_git clone and use kubectx to easily switch between contexts_

```bash
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx
```

_git clone and use kubens to easily switch between namespaces_

```bash
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens
```

_Use the CustomResourceDefinition object to create custom objects/resources_
_Will need a custom controller defined as well_
_Can package both using an operator framework_
