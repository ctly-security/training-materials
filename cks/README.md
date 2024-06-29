# Certified Kubernetes Security (CKS)

CKS course and exam preparation:
https://kodekloud.com/learning-path/cks/ 

Ensure that you can complete all mock exams without looking at the answer.

After that, do the killer.sh exam simulation, which is part of your exam registration. it is super hard hence understand the questions, answers and concept accordingly.

Good Reference:
- TBD

## Domains & Competencies

### Cluster Setup 10%
- Use Network security policies to restrict cluster level access 
- Use CIS benchmark to review the security configuration of Kubernetes components (etcd, kubelet, kubedns, kubeapi)
- Properly set up Ingress objects with security control 
- Protect node metadata and endpoints 
- Minimize use of, and access to, GUI elements 
- Verify platform binaries before deploying

### System Hardening 15%
- Minimize host OS footprint (reduce attack surface)
- Minimize IAM roles 
- Minimize external access to the network 
- Appropriately use kernel hardening tools such as AppArmor, seccomp

### Minimize Microservice Vulnerabilities 20%
- Setup appropriate OS level security domains 
- Manage Kubernetes secrets 
- Use container runtime sandboxes in multi-tenant environments (e.g. gvisor, kata containers)
- Implement pod to pod encryption by use of mTLS

### Supply Chain Security 20%
- Minimize base image footprint 
- Secure your supply chain: whitelist allowed registries, sign and validate images 
- Use static analysis of user workloads (e.g.Kubernetes resources, Docker files)
- Scan images for known vulnerabilities

### Monitoring, Logging and Runtime Security 20%
- Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities 
- Detect threats within physical infrastructure, apps, networks, data, users and workloads 
- Detect all phases of attack regardless where it occurs and how it spreads 
- Perform deep analytical investigation and identification of bad actors within environment 
- Ensure immutability of containers at runtime
- Use Audit Logs to monitor access

## Useful Commands to Remember

### App Armor
#### To Load App Armor Profile
```
apparmor_parser -q /etc/apparmor.d/frontend
```

#### To check
```
aa-status
```

#### setting at pod
```
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    # Tell Kubernetes to apply the AppArmor profile "k8s-apparmor-example-deny-write".
    container.apparmor.security.beta.kubernetes.io/<CONTAINER_NAME>: localhost/<APPARMOR_PROFILENAME>
spec:
  containers:
```

### Secret
#### Get secret
```
kubectl get secret -o yaml
echo "bjBPbjNDQG5IQGNrTTM=" | base64 --decode
```

### Runtime Classes
#### Define gvisor
```
apiVersion: v1
kind: Pod
metadata:
    name: busy-rx100
    namespace: production
spec:
    runtimeClassName: gvisor
    containers:
```

### Falco
##### Enable File-output

```
edit /etc/falco/falco.yaml

file_output:
  enabled: true
  keep_alive: false
  filename: /opt/security_incidents/alerts.log
```

#### update Falco rules

```
/etc/falco/falco_rules.local.yaml

- rule: Write below binary dir
  desc: an attempt to write to any file below a set of binary directories
  condition: >
    bin_dir and evt.dir = < and open_write
    and not package_mgmt_procs
    and not exe_running_docker_save
    and not python_running_get_pip
    and not python_running_ms_oms
    and not user_known_write_below_binary_dir_activities
  output: >
    File below a known binary directory opened for writing (user_id=%user.uid file_updated=%fd.name command=%proc.cmdline)
  priority: CRITICAL
  tags: [filesystem, mitre_persistence]
```

#### restart
```
systemctl restart falco
```

#### read falco logs
```
    find pod running with image nginx that have unwanted package
    cat /var/log/syslog | grep falco | grep nginx
    crictl ps -id <container id>
    crictl pods -id <container id>
```

### Admission controller
#### Create new admission configuration

```
apiVersion: apiserver.config.k8s.io/v1
kind: AdmissionConfiguration
plugins:
- name: ImagePolicyWebhook
  configuration:
    imagePolicy:
      kubeConfigFile: /etc/admission-controllers/admission-kubeconfig.yaml
      allowTTL: 50
      denyTTL: 50
      retryBackoff: 500
      defaultAllow: false
```

#### enable admission controller
```
in kube-apiserver.yaml:

- --admission-control-config-file=/etc/admission-controllers/admission-configuration.yaml
- --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
```

### Audit Policy
#### create audit policy

```
apiVersion: audit.k8s.io/v1
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # Log pod changes at RequestResponse level
  - level: RequestResponse
    resources:
    - group: ""
      resources: ["pods"]
  - level: Metadata
    resources:
    - group: ""
      resources: ["pods/log", "pods/status"]

  # Don't log requests to a configmap called "controller-leader"
  - level: None
    resources:
    - group: ""
      resources: ["configmaps"]
      resourceNames: ["controller-leader"]

  # Don't log watch requests by the "system:kube-proxy" on endpoints or services
  - level: None
    users: ["system:kube-proxy"]
    verbs: ["watch"]
    resources:
    - group: "" # core API group
      resources: ["endpoints", "services"]

  # Don't log authenticated requests to certain non-resource URL paths.
  - level: None
    userGroups: ["system:authenticated"]
    nonResourceURLs:
    - "/api*" # Wildcard matching.
    - "/version"

  # Log the request body of configmap changes in kube-system.
  - level: Request
    resources:
    - group: "" # core API group
      resources: ["configmaps"]
    # This rule only applies to resources in the "kube-system" namespace.
    # The empty string "" can be used to select non-namespaced resources.
    namespaces: ["kube-system"]

  # Log configmap and secret changes in all other namespaces at the Metadata level.
  - level: Metadata
    resources:
    - group: "" # core API group
      resources: ["secrets", "configmaps"]

  # Log all other resources in core and extensions at the Request level.
  - level: Request
    resources:
    - group: "" # core API group
    - group: "extensions" # Version of group should NOT be included.

  # A catch-all rule to log all other requests at the Metadata level.
  - level: Metadata
    # Long-running requests like watches that fall under this rule will not
    # generate an audit event in RequestReceived.
    omitStages:
      - "RequestReceived"
```

#### config kube-apiserver

add the following in /etc/kubernetes/manifest/kube-apiserver.yaml
```
- --audit-policy-file=/etc/kubernetes/audit-policy.yaml
- --audit-log-path=/var/log/kubernetes/audit/audit.log
```

#### volume

add the following in /etc/kubernetes/manifest/kube-apiserver.yaml
```
volumeMounts:
  - mountPath: /etc/kubernetes/audit-policy.yaml
    name: audit
    readOnly: true
  - mountPath: /var/log/kubernetes/audit/
    name: audit-log
    readOnly: false
    
volumes:
- name: audit
  hostPath:
    path: /etc/kubernetes/audit-policy.yaml
    type: File

- name: audit-log
  hostPath:
    path: /var/log/kubernetes/audit/
    type: DirectoryOrCreate
```

### where to read APIserver logs

```
wacth kube-apiserver process
crictl ps

cat /var/log/pods/kube-system_kube-apiserver-controlplane_<RANDOM>....

cat /var/log/syslog
```

### Create new key
openssl genrsa -out 60099.key 2048
openssl req -new -key 60099.key -out 60099.csr
#### 

### Kube-bench
#### target specific benchmark
```
kube-bench --check=1.1.1
```