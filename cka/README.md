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

#### Create a service named redis-service of type ClusterIP to expose pod redis on port 6379
```yaml
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```
(This will automatically use the pod's labels as selectors)

Or
```yaml
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
```

(This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

#### Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
```
kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml
```

(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

```
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml
```

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the kubectl expose command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

### Create a daemonSet
first create a deployment:

```
kubectl create deployment elasticsearch --image=k8s.gcr.io/fluentd-elasticsearch:1.20 --dry-run=client -o yaml > fluend.yaml
```

Then open the file, change kind to "daemonSet", and remove:
- replicas
- strategy
- status

### static pod
check process to see where is the static yaml files located:
```
ps -ef | grep kubelet
```
### Performance monitoring
```
git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
cd kubernetes-metrics-server
kubectl create -f .

kubectl top node
kubectl top pod
```

### Viewing Logs
```
kubectl logs -f webapp-1
```

### rolling update and rollback
```
# to implicit update (not recommended)
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1

kubectl rollout status deployment/myapp-deployment

kubectl rollout history deployment/myapp-deployment
kubectl rollout undo deployment/myapp-deployment
```

#### other commands
```
# pods
kubectl get pods -o wide -n=dev --selector tier=front-end,env=dev,bu=finance
kubectl delete pod <pod_name> 
kubectl describe pod <pod_name> 
kubectl run nginx --image=nginx  

kubectl run redis --image=redis123 --dry-run=client -o yaml --command -- sleep 1000 > testing.yaml 

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
kubectl get pods --all-namespaces

# to connect to a service in another namespace:
{name of service}.{namespace}.svv.cluster.local
example: db-service.dev.svc.cluster.local

# to switch to DEV namespace
kubectl config set-context $(kubectl config curent-context) --namespace=dev

# taint node
kubectl taint node node01 KEY=VARIABLE:EFFECT
example: kubectl taint node node01 spray=mortein:NoSchedule

# label
kubectl label node node01 color=blue

# node affinity
spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
      containers:
 
OR

spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: In
                values:
                - blue
      containers:
```

### Example of pod.yaml with tolerations: 
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
  tolerations:
    - key: spray
      value: mortein
      effect: NoSchedule
      operator: Equal
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
