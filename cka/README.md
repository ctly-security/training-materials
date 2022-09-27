# Kurbernetes

## Certified Kubernetes Administrator (CKA)

### Beginner course for Kubernetes and Container
https://prudentialservices.udemy.com/course/learn-kubernetes 

CKA course and exam preparation:
https://prudentialservices.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/ 

Once you passed CKA, you can take for Certified Kubernetes Security Specialist (CKS). 

## Certified Kubernetes Security Specialist (CKS)

TBD

### References
https://prudentialservices.udemy.com/course/learn-docker/learn/lecture/15828702#overview 

## Commands

kubectl get pods -o wide 

kubectl delete pod <pod_name> 

kubectl describe pod <pod_name> 

Kubectl run nginx --image=nginx  

kubectl run redis --image=redis123 --dry-run=client -o yaml > testing.yaml 

kubectl create -f pod-definition.yaml 

kubectl delete -f pod-definition.yaml 

kubectl apply -f pod-definition.yaml 

kubectl get replicationcontroller 

kubectl get replicaset 

kubectl replace -f replicaset-definition.yaml 

kubectl scale --replicas=6 -f replicaset-definition.yaml 

kubectl scale --replicas=6 replicaset myapp-replicaset 

### Example of pod.yaml: 
```yaml pod.yaml:
apiVersion: v1 
kind: Pod 
metadata: 
  name: webserver 
  type: front-end 
spec: 
  containers: 
    - name: nginx 
      image: nginx
```

Example of Replication Controller 

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

 

Replica set 

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
