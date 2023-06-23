# Certified Kubernetes Administrator (CKA)

## Beginner course for Kubernetes and Container
https://prudentialservices.udemy.com/course/learn-kubernetes 

CKA course and exam preparation:
https://prudentialservices.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/ 

Ensure that you can complete all mock exams without looking at the answer.

After that, do the killer.sh exam simulation, which is part of your exam registration. it is super hard hence understand the questions, answers and concept accordingly.

Good Reference:
- https://www.youtube.com/watch?v=e-vxYtSFOns&t=2165s&ab_channel=DevOpsMadeEasy
- https://www.youtube.com/watch?v=8fFcx9EgWvk&t=191s&ab_channel=%EC%B5%9C%EA%B4%91%EB%AF%BC
- https://www.youtube.com/watch?v=8VK9NJ3pObU&t=9s&ab_channel=jedi.ascode%28%29
- https://gengwg.blogspot.com/2022/02/cka-simulator-kubernetes-122.html

Once you passed CKA, you can take for Certified Kubernetes Security Specialist (CKS). 

## Domains & Competencies

### Storage 10%
- Storage Classes
- PV
- PVC
- Access modes
- Resizing
- Mounting

### Troubleshooting 30%
- Node logging
- monitor applications
- container stdout
- container stderr logs
- troubleshoot application
- cluster failures
- troubleshoot networking

### Workloads & Scheduling 15%
- deployments,
- rolling update and rollbacks
- Using ConfigMaps and Secrets
- Scale applications
- resource limits
- static pods
- daemonSets

### Cluster architecture, installation and configuration 25%
- Manage RBAC
- using kubeadm to install a basic cluster
- perform a version upgrade
- implement etcd backup and restore

### Services and networking 20%
- understand networking between nodes & pods
- expose application using clusterIP
- nodePort, LB, ingress resources, ingress controllers
- cni plugins
- endpoint objects

## Common Questions
- ETCD Backup & Restore
- Upgrade cluster to v1.2.x.x
- RBAC: ClusterRole, ClusterRoleBindings, Role, RoleBinding, Service Accounts
- Find nodes that are ready and taints not set as NoSchedule
- Find Pod that is consuming maximum CPU from all the pods in a namespace
- add a sidecar container that prints the logs of main container. you need to create `emptyDir` and mount to both the containers. Command to print logs will be given.
- Create a PVC of 10Mi and mount it to a container. Later patch PVC to 70Mi.
- create a pod with 2 containers. image names and other info will be provided.
- scale a deployment to 3 replicas
- create a deployment with Ximage and Ytag. change the Y tag to Z and record the changes
- create a pod and manually schedule it with NodeSelector(disk: ssd)
- schedule a Pod on master node without modifying labels on the node
- Drain all workloads from a node
- create a network policy that allows all pods in Y namespace to talk to all Pods in X namespace on port 5432
- a kubernetes worker node is in state NotReady. Investigate why this is the case and perform any appropriates steps to bring the node to a ready state
- setting CPU and Mem limits to containers of a Pod 
- security contexts to add user id and group id to pod, add additional capabilities like SYS_TIME and NET_ADMIN
- sort all pods by their order of creation
- write all namespaced resource to a file
- filter pods by certain labels and write the results to a file
- delete kube-proxy container from Y node. use crictl ps and crictl rm
- create a pod with given labels
- add a secondary container that runs certain commands like sleep 2000
- write a command to list all clusters from kubeconfig file into file
- create a pod with some environment variables
- mount an existing secret as a volume

## Useful Commands to Remember

### Create an NGINX Pod
```
kubectl run nginx --image=nginx
```

### Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
```
kubectl run nginx --image=nginx --dry-run=client -o yaml
```

### Create a deployment
```
kubectl create deployment --image=nginx nginx
```

### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
```
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
```

### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
```
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
```

### Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.
```
kubectl create -f nginx-deployment.yaml
```

### Create a service named redis-service of type ClusterIP to expose pod redis on port 6379
```yaml
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```
(This will automatically use the pod's labels as selectors)

Or
```yaml
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
```

(This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

### Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
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

### Troubleshooting
## Cluster 
for cluster, check the components if they are running with kubectl -n kube-system.

most kubesystem files are in /etc/kubernetes/manifest

## Node
check nodes first with:
```
kubectl get nodes
```

can also check node with:
```
kubectl describe node <NODE NAME>
```

if error, ssh into the node and check kubelet:
```
service kubelet status
```

you can use the following to see node logs:
```
journalctl -u kubelet
```

service kubelet yaml file is in:
```
/var/lib/kubelet/....

for the config file itself:
/etc/kubernetes/kubelet.conf
```

### References
https://prudentialservices.udemy.com/course/learn-docker/learn/lecture/15828702#overview
