# Kurbernetes

## Certified Kubernetes Administrator (CKA)

### Beginner course for Kubernetes and Container
https://prudentialservices.udemy.com/course/learn-kubernetes 

CKA course and exam preparation:
https://prudentialservices.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/ 

Once you passed CKA, you can take for Certified Kubernetes Security Specialist (CKS). 

### Useful Commands to Remember

#### Create an NGINX Pod
```
kubectl run nginx --image=nginx
```

#### Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
```
kubectl run nginx --image=nginx --dry-run=client -o yaml
```

#### Create a deployment
```
kubectl create deployment --image=nginx nginx
```

#### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
```
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
```

#### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
```
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
```

#### Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.
```
kubectl create -f nginx-deployment.yaml
```

#### other commands
```
# pods
kubectl get pods -o wide --namespace=dev
kubectl delete pod <pod_name> 
kubectl describe pod <pod_name> 
kubectl run nginx --image=nginx  

kubectl run redis --image=redis123 --dry-run=client -o yaml > testing.yaml 

# definition
kubectl create -f pod-definition.yaml 
kubectl delete -f pod-definition.yaml 
kubectl apply -f pod-definition.yaml 
kubectl get replicationcontroller 

# Replica Set
kubectl get replicaset
kubectl replace -f replicaset-definition.yaml 
kubectl scale --replicas=6 -f replicaset-definition.yaml 
kubectl scale --replicas=6 replicaset myapp-replicaset

# Namespace
kubectl create -f namespace-dev.yml
kubectl create namespace dev

# to switch to DEV namespace
kubectl config set-context $(kubectl config curent-context) --namespace=dev

kubectl get pods --all-namespaces
```

### Example of pod.yaml: 
```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: webserver 
  type: front-end
  namespace: dev
spec: 
  containers: 
    - name: nginx 
      image: nginx
```

### Example of Replication Controller:
```yaml
apiVersion: v1 
kind: ReplicationController 
metadata: 
  name: myapp-rc 
  labels: 
    app: myapp 
    type: front-end 
spec: 
  template: 
    metadata: 
      name: myapp-pod 
      labels: 
        app: myapp 
        type: front-end 
   spec: 
     containers: 
       - name: nginx-container 
         image: nginx
  replicas: 3 
```

### Example of Replica set: 
```yaml
apiVersion: apps/v1 
kind: ReplicaSet 
metadata: 
  name: myapp-replicaset 
  labels: 
    app: myapp 
    type: front-end 
spec: 
  template: 
    metadata: 
      name: myapp-pod 
      labels: 
        app: myapp 
        type: front-end 
    spec: 
      containers: 
        - name: nginx-container 
           image: nginx 
    replicas: 3 
    selector: 
      matchLabels: 
        type: front-end 
```

### Example of Service (NodePort)
```yaml
apiVersion : v1
kind: Service
metadata:
  name: myapp-service
  
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: myapp
    type: front-end
```

### Example of Service (ClusterIP)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: back-end

spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  
  selector:
    app: myapp
    type: back-end
```

### Example of Service (LoadBalancer, only for GCP, Azure and AWS)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service

spec:
  type: LoadBalancer
  ports;
    - targetPort: 80
      port: 80
      nodePort: 30008
```

### Example of namespace
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

### Example of resource quota
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
  
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

## Certified Kubernetes Security Specialist (CKS)

TBD

### References
https://prudentialservices.udemy.com/course/learn-docker/learn/lecture/15828702#overview
