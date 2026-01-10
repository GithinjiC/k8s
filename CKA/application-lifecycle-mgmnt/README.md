### Rollout
```bash
kubectl create -f deployment.yml
kubectl get deployments
kubectl apply -f deployment.yml
kubectl set image deployment/myapp-deployment <container_name>=nginx:1.9.1
kubectl rollout status deployment/myapp-deployment
kubectl rollout history deployment/myapp-deployment
kubectl rollout undo deployment/myapp-deployment
```

### Deployment Strategies
1. Recreate
2. Rolling Update(default)

### Env Variables
*Use `spec.containers.env`*  
*For configMaps use `env.valueFrom.configMapKeyRef`*  
*For secrets use `env.valueFrom.secretKeyRef`*  
*Secrets and ConfigMaps can be mounted as files. Each secret is created as separate file*
```bash
# imperative method
kubectl create configmap <config_name> \
    --from-literal=<key>=value

kubectl create secret generic <secret_name> \
    --from-literal=<key>=value
# Example
kubectl create configmap app-config \
    --from-literal=APP_COLOR=blue \
    --from-literal=APP_MOD=prod

kubectl create configmap app-config \
    --from-file=app_config.properties \

kubectl create secret generic app-secret \
    --from-literal=DB_HOST=mysql

kubectl create secret generic app-secret \
    --from-file=app_secret.properties
```

```bash
# Declarative method
kubectl create -f configmap.yml
kubectl get configmaps
kubectl describe configmaps <config_name>

kubectl create -f secret.yml
kubectl get secrets
kubect describe secret <secret_name>
# Use base64 to encode
echo -n 'DB_HOST' | base64 --encode
```

### Autoscaling
*Vertical Scaling - inc resources i.e cpu, memory*  
*Horizontal Scaling inc instances*
```bash
# To create a HPA imperatively
kubectl autoscale deployment my-app --cpu-percent=50 --min=1 --max=10
kubectl get hpa
kubectl delete hpa my-app
```
*HPA is built in, VPA is not*  
*Check if In-Place Update of Pod Resources is available*