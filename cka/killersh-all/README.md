# Killer.sh

## Questions and Answers

### Question 01 | Contexts

Task weight: 1%

You have access to muliple clusters from your main terminal through `kubectl` contexts. Write all those context names into `/opt/course/1/contexts` .

Next write a command to display the current context into `/opt/course/1/context_default_kubectl.sh`, the command should use `kubectl`.

Finally write a second command doing the same thing into `/opt/course/1/context_default_no_kubecth.sh`, but without the use of `kubectl`.

#### Answer

```bash
export do="--dry-run=client -o yaml"

k config get-contexts -o name

k config get-contexts -o name > /opt/course/1/contexts

kubectl config current-context

vi /opt/course/1/context_default_kubectl.sh

kubectl config current-context

vi /opt/course/1/context_default_no_kubectl.sh

cat ~/.kube/config | grep current-context | awk -F ' ' '{print $2}'
```

### Question 02 | Schedule Pod on Master Node

Task weight: 3%

Use context: kubectl config use-context k8s-c1-H

Create a single *Pod* of image `httpd:2.4.41-alpine` in *Namespace* `default`. The *Pod* should be named `pod1` and the container should be named `pod1-container`. This *Pod* should **only** be scheduled on a master *node*, do not add new labels any nodes.

#### Answer

```bash
kubectl config use-context k8s-c1-H

k run pod1 --image httpd:2.4.41-alpine $do > 2.yaml

vi 2.yaml
```

### Question 03 | Scale down StatefulSet

Task weight: 1%

Use context: kubectl config use-context k8s-c1-H

There are two `Pods` named `o3db-*` in Namespace `project-c13`. C13 management asked you to scale the `Pods` down to one replica to save resources. Record the action.

#### Answer

### Question 04 | Pod Ready if Service is reachable

Task weight: 4%

Use context: kubectl config use-context k8s-c1-H

 

Do the following in Namespace default. Create a single Pod named ready-if-service-ready of image nginx:1.16.1-alpine. Configure a LivenessProbe which simply runs true. Also configure a ReadinessProbe which does check if the url http://service-am-i-ready:80 is reachable, you can use wget -T2 -O- http://service-am-i-ready:80 for this. Start the Pod and confirm it isn't ready because of the ReadinessProbe.

Create a second Pod named am-i-ready of image nginx:1.16.1-alpine with label id: cross-server-ready. The already existing Service service-am-i-ready should now have that second Pod as endpoint.

Now the first Pod should be in ready state, confirm that.

#### Answer

### Question 05 | Kubectl sorting

Task weight: 1%

 

Use context: kubectl config use-context k8s-c1-H

 

There are various Pods in all namespaces. Write a command into /opt/course/5/find_pods.sh which lists all Pods sorted by their AGE (metadata.creationTimestamp).

Write a second command into /opt/course/5/find_pods_uid.sh which lists all Pods sorted by field metadata.uid. Use kubectl sorting for both commands.

#### Answer

### Question 6 | Storage, PV, PVC, Pod volume

Task weight: 8%

 

Use context: kubectl config use-context k8s-c1-H

 

Create a new PersistentVolume named safari-pv. It should have a capacity of 2Gi, accessMode ReadWriteOnce, hostPath /Volumes/Data and no storageClassName defined.

Next create a new PersistentVolumeClaim in Namespace project-tiger named safari-pvc . It should request 2Gi storage, accessMode ReadWriteOnce and should not define a storageClassName. The PVC should bound to the PV correctly.

Finally create a new Deployment safari in Namespace project-tiger which mounts that volume at /tmp/safari-data. The Pods of that Deployment should be of image httpd:2.4.41-alpine.

#### Answer

### Question 7 | Node and Pod Resource Usage

Task weight: 1%

 

Use context: kubectl config use-context k8s-c1-H

 

The metrics-server hasn't been installed yet in the cluster, but it's something that should be done soon. Your college would already like to know the kubectl commands to:

show node resource usage
show Pod and their containers resource usage
Please write the commands into /opt/course/7/node.sh and /opt/course/7/pod.sh.

#### Answer

### Question 08 | Get Master Information

ask weight: 2%

 

Use context: kubectl config use-context k8s-c1-H

 

Ssh into the master node with ssh cluster1-master1. Check how the master components kubelet, kube-apiserver, kube-scheduler, kube-controller-manager and etcd are started/installed on the master node. Also find out the name of the DNS application and how it's started/installed on the master node.

Write your findings into file /opt/course/8/master-components.txt. The file should be structured like:

# /opt/course/8/master-components.txt
kubelet: [TYPE]
kube-apiserver: [TYPE]
kube-scheduler: [TYPE]
kube-controller-manager: [TYPE]
etcd: [TYPE]
dns: [TYPE] [NAME]
Choices of [TYPE] are: not-installed, process, static-pod, pod

#### Answer

### Question 9 | Kill Scheduler, Manual Scheduling

Task weight: 5%

 

Use context: kubectl config use-context k8s-c2-AC

 

Ssh into the master node with ssh cluster2-master1. Temporarily stop the kube-scheduler, this means in a way that you can start it again afterwards.

Create a single Pod named manual-schedule of image httpd:2.4-alpine, confirm its created but not scheduled on any node.

Now you're the scheduler and have all its power, manually schedule that Pod on node cluster2-master1. Make sure it's running.

Start the kube-scheduler again and confirm its running correctly by creating a second Pod named manual-schedule2 of image httpd:2.4-alpine and check if it's running on cluster2-worker1.

#### Answer

### Question 10 | RBAC ServiceAccount Role RoleBinding
Task weight: 6%

 

Use context: kubectl config use-context k8s-c1-H

 

Create a new ServiceAccount processor in Namespace project-hamster. Create a Role and RoleBinding, both named processor as well. These should allow the new SA to only create Secrets and ConfigMaps in that Namespace.

#### Answer

### Question 11 | DaemonSet on all Nodes
Task weight: 4%

 

Use context: kubectl config use-context k8s-c1-H

 

Use Namespace project-tiger for the following. Create a DaemonSet named ds-important with image httpd:2.4-alpine and labels id=ds-important and uuid=18426a0b-5f59-4e10-923f-c0e078e82462. The Pods it creates should request 10 millicore cpu and 10 mebibyte memory. The Pods of that DaemonSet should run on all nodes, master and worker.

#### Answer

### Question 12 | Deployment on all Nodes
Task weight: 6%

 

Use context: kubectl config use-context k8s-c1-H

 

Use Namespace project-tiger for the following. Create a Deployment named deploy-important with label id=very-important (the Pods should also have this label) and 3 replicas. It should contain two containers, the first named container1 with image nginx:1.17.6-alpine and the second one named container2 with image kubernetes/pause.

There should be only ever one Pod of that Deployment running on one worker node. We have two worker nodes: cluster1-worker1 and cluster1-worker2. Because the Deployment has three replicas the result should be that on both nodes one Pod is running. The third Pod won't be scheduled, unless a new worker node will be added.

In a way we kind of simulate the behaviour of a DaemonSet here, but using a Deployment and a fixed number of replicas.

#### Answer

### Question 13 | Multi Containers and Pod shared Volume
Task weight: 4%

 

Use context: kubectl config use-context k8s-c1-H

 

Create a Pod named multi-container-playground in Namespace default with three containers, named c1, c2 and c3. There should be a volume attached to that Pod and mounted into every container, but the volume shouldn't be persisted or shared with other Pods.

Container c1 should be of image nginx:1.17.6-alpine and have the name of the node where its Pod is running on value available as environment variable MY_NODE_NAME.

Container c2 should be of image busybox:1.31.1 and write the output of the date command every second in the shared volume into file date.log. You can use while true; do date >> /your/vol/path/date.log; sleep 1; done for this.

Container c3 should be of image busybox:1.31.1 and constantly send the content of file date.log from the shared volume to stdout. You can use tail -f /your/vol/path/date.log for this.

Check the logs of container c3 to confirm correct setup.

#### Answer

### Question 14 | Find out Cluster Information
Task weight: 2%

 

Use context: kubectl config use-context k8s-c1-H

 

You're ask to find out following information about the cluster k8s-c1-H:

How many master nodes are available?
How many worker nodes are available?
What is the Service CIDR?
Which Networking (or CNI Plugin) is configured and where is its config file?
Which suffix will static pods have that run on cluster1-worker1?
Write your answers into file /opt/course/14/cluster-info, structured like this:

# /opt/course/14/cluster-info
1: [ANSWER]
2: [ANSWER]
3: [ANSWER]
4: [ANSWER]
5: [ANSWER]

#### Answer

### Question 15 | Cluster Event Logging
Task weight: 3%

 

Use context: kubectl config use-context k8s-c2-AC

 

Write a command into /opt/course/15/cluster_events.sh which shows the latest events in the whole cluster, ordered by time. Use kubectl for it.

Now kill the kube-proxy Pod running on node cluster2-worker1 and write the events this caused into /opt/course/15/pod_kill.log.

Finally kill the containerd container of the kube-proxy Pod on node cluster2-worker1 and write the events into /opt/course/15/container_kill.log.

Do you notice differences in the events both actions caused?

#### Answer

### Question 16 | Namespaces and Api Resources
Task weight: 2%

 

Use context: kubectl config use-context k8s-c1-H

 

Create a new Namespace called cka-master.

Write the names of all namespaced Kubernetes resources (like Pod, Secret, ConfigMap...) into /opt/course/16/resources.txt.

Find the project-* Namespace with the highest number of Roles defined in it and write its name and amount of Roles into /opt/course/16/crowded-namespace.txt.

#### Answer

### Question 17 | Find Container of Pod and check info
Task weight: 3%

 

Use context: kubectl config use-context k8s-c1-H

 

In Namespace project-tiger create a Pod named tigers-reunite of image httpd:2.4.41-alpine with labels pod=container and container=pod. Find out on which node the Pod is scheduled. Ssh into that node and find the containerd container belonging to that Pod.

Using command crictl:

Write the ID of the container and the info.runtimeType into /opt/course/17/pod-container.txt
Write the logs of the container into /opt/course/17/pod-container.log

#### Answer

### Question 18 | Fix Kubelet
Task weight: 8%

 

Use context: kubectl config use-context k8s-c3-CCC

 

There seems to be an issue with the kubelet not running on cluster3-worker1. Fix it and confirm that cluster has node cluster3-worker1 available in Ready state afterwards. You should be able to schedule a Pod on cluster3-worker1 afterwards.

Write the reason of the issue into /opt/course/18/reason.txt.

#### Answer

### Question 19 | Create Secret and mount into Pod
Task weight: 3%

 

Use context: kubectl config use-context k8s-c3-CCC

 

Do the following in a new Namespace secret. Create a Pod named secret-pod of image busybox:1.31.1 which should keep running for some time. It should be able to run on master nodes as well, create the proper toleration.

There is an existing Secret located at /opt/course/19/secret1.yaml, create it in the secret Namespace and mount it readonly into the Pod at /tmp/secret1.

Create a new Secret in Namespace secret called secret2 which should contain user=user1 and pass=1234. These entries should be available inside the Pod's container as environment variables APP_USER and APP_PASS.

Confirm everything is working.

#### Answer

### Question 20 | Update Kubernetes Version and join cluster
Task weight: 10%

 

Use context: kubectl config use-context k8s-c3-CCC

 

Your coworker said node cluster3-worker2 is running an older Kubernetes version and is not even part of the cluster. Update Kubernetes on that node to the exact version that's running on cluster3-master1. Then add this node to the cluster. Use kubeadm for this.

#### Answer

### Question 21 | Create a Static Pod and Service
Task weight: 2%

 

Use context: kubectl config use-context k8s-c3-CCC

 

Create a Static Pod named my-static-pod in Namespace default on cluster3-master1. It should be of image nginx:1.16-alpine and have resource requests for 10m CPU and 20Mi memory.

Then create a NodePort Service named static-pod-service which exposes that static Pod on port 80 and check if it has Endpoints and if its reachable through the cluster3-master1 internal IP address. You can connect to the internal node IPs from your main terminal.

#### Answer

### Question 22 | Check how long certificates are valid
Task weight: 2%

 

Use context: kubectl config use-context k8s-c2-AC

 

Check how long the kube-apiserver server certificate is valid on cluster2-master1. Do this with openssl or cfssl. Write the exipiration date into /opt/course/22/expiration.

Also run the correct kubeadm command to list the expiration dates and confirm both methods show the same date.

Write the correct kubeadm command that would renew the apiserver server certificate into /opt/course/22/kubeadm-renew-certs.sh.

#### Answer

### Question 23 | Kubelet client/server cert info
Task weight: 2%

 

Use context: kubectl config use-context k8s-c2-AC

 

Node cluster2-worker1 has been added to the cluster using kubeadm and TLS bootstrapping.

Find the "Issuer" and "Extended Key Usage" values of the cluster2-worker1:

kubelet client certificate, the one used for outgoing connections to the kube-apiserver.
kubelet server certificate, the one used for incoming connections from the kube-apiserver.
Write the information into file /opt/course/23/certificate-info.txt.

Compare the "Issuer" and "Extended Key Usage" fields of both certificates and make sense of these.

#### Answer

### Question 24 | NetworkPolicy
Task weight: 9%

 

Use context: kubectl config use-context k8s-c1-H

 

There was a security incident where an intruder was able to access the whole cluster from a single hacked backend Pod.

To prevent this create a NetworkPolicy called np-backend in Namespace project-snake. It should allow the backend-* Pods only to:

connect to db1-* Pods on port 1111
connect to db2-* Pods on port 2222
Use the app label of Pods in your policy.

After implementation, connections from backend-* Pods to vault-* Pods on port 3333 should for example no longer work.

#### Answer

### Question 25 | Etcd Snapshot Save and Restore

ask weight: 8%

 

Use context: kubectl config use-context k8s-c3-CCC

 

Make a backup of etcd running on cluster3-master1 and save it on the master node at /tmp/etcd-backup.db.

Then create a Pod of your kind in the cluster.

Finally restore the backup, confirm the cluster is still working and that the created Pod is no longer with us.

#### Answer

### Extra Question 1 | Find Pods first to be terminated
Use context: kubectl config use-context k8s-c1-H

 

Check all available Pods in the Namespace project-c13 and find the names of those that would probably be terminated first if the nodes run out of resources (cpu or memory) to schedule all Pods. Write the Pod names into /opt/course/e1/pods-not-stable.txt.

#### Answer

### Extra Question 2 | Curl Manually Contact API
Use context: kubectl config use-context k8s-c1-H

 

There is an existing ServiceAccount secret-reader in Namespace project-hamster. Create a Pod of image curlimages/curl:7.65.3 named tmp-api-contact which uses this ServiceAccount. Make sure the container keeps running.

Exec into the Pod and use curl to access the Kubernetes Api of that cluster manually, listing all available secrets. You can ignore insecure https connection. Write the command(s) for this into file /opt/course/e4/list-secrets.sh.


#### Answer
