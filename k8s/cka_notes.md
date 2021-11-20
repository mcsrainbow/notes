## CKA Notes

### kubectl auto completion

```
[centos@kubeadm01 ~]$ sudo yum install -y bash-completion

[centos@kubeadm01 ~]$ cat >> ~/.bashrc <<EOF
# kubectl auto completion
source <(kubectl completion bash)
complete -F __start_kubectl kubectl
EOF

[centos@kubeadm01 ~]$ source ~/.bashrc
```

### Kubernetes resources

```
[centos@kubeadm01 ~]$ kubectl api-resources
NAME                            SHORTNAMES APIVERSION                           NAMESPACED KIND
bindings                                   v1                                   true       Binding
componentstatuses               cs         v1                                   false      ComponentStatus
configmaps                      cm         v1                                   true       ConfigMap
endpoints                       ep         v1                                   true       Endpoints
events                          ev         v1                                   true       Event
limitranges                     limits     v1                                   true       LimitRange
namespaces                      ns         v1                                   false      Namespace
nodes                           no         v1                                   false      Node
persistentvolumeclaims          pvc        v1                                   true       PersistentVolumeClaim
persistentvolumes               pv         v1                                   false      PersistentVolume
pods                            po         v1                                   true       Pod
podtemplates                               v1                                   true       PodTemplate
replicationcontrollers          rc         v1                                   true       ReplicationController
resourcequotas                  quota      v1                                   true       ResourceQuota
secrets                                    v1                                   true       Secret
serviceaccounts                 sa         v1                                   true       ServiceAccount
services                        svc        v1                                   true       Service
mutatingwebhookconfigurations              admissionregistration.k8s.io/v1      false      MutatingWebhookConfiguration
validatingwebhookconfigurations            admissionregistration.k8s.io/v1      false      ValidatingWebhookConfiguration
customresourcedefinitions       crd,crds   apiextensions.k8s.io/v1              false      CustomResourceDefinition
apiservices                                apiregistration.k8s.io/v1            false      APIService
controllerrevisions                        apps/v1                              true       ControllerRevision
daemonsets                      ds         apps/v1                              true       DaemonSet
deployments                     deploy     apps/v1                              true       Deployment
replicasets                     rs         apps/v1                              true       ReplicaSet
statefulsets                    sts        apps/v1                              true       StatefulSet
tokenreviews                               authentication.k8s.io/v1             false      TokenReview
localsubjectaccessreviews                  authorization.k8s.io/v1              true       LocalSubjectAccessReview
selfsubjectaccessreviews                   authorization.k8s.io/v1              false      SelfSubjectAccessReview
selfsubjectrulesreviews                    authorization.k8s.io/v1              false      SelfSubjectRulesReview
subjectaccessreviews                       authorization.k8s.io/v1              false      SubjectAccessReview
horizontalpodautoscalers        hpa        autoscaling/v1                       true       HorizontalPodAutoscaler
cronjobs                        cj         batch/v1                             true       CronJob
jobs                                       batch/v1                             true       Job
certificatesigningrequests      csr        certificates.k8s.io/v1               false      CertificateSigningRequest
leases                                     coordination.k8s.io/v1               true       Lease
endpointslices                             discovery.k8s.io/v1                  true       EndpointSlice
events                          ev         events.k8s.io/v1                     true       Event
flowschemas                                flowcontrol.apiserver.k8s.io/v1beta1 false      FlowSchema
prioritylevelconfigurations                flowcontrol.apiserver.k8s.io/v1beta1 false      PriorityLevelConfiguration
ingressclasses                             networking.k8s.io/v1                 false      IngressClass
ingresses                       ing        networking.k8s.io/v1                 true       Ingress
networkpolicies                 netpol     networking.k8s.io/v1                 true       NetworkPolicy
runtimeclasses                             node.k8s.io/v1                       false      RuntimeClass
poddisruptionbudgets            pdb        policy/v1                            true       PodDisruptionBudget
podsecuritypolicies             psp        policy/v1beta1                       false      PodSecurityPolicy
clusterrolebindings                        rbac.authorization.k8s.io/v1         false      ClusterRoleBinding
clusterroles                               rbac.authorization.k8s.io/v1         false      ClusterRole
rolebindings                               rbac.authorization.k8s.io/v1         true       RoleBinding
roles                                      rbac.authorization.k8s.io/v1         true       Role
priorityclasses                 pc         scheduling.k8s.io/v1                 false      PriorityClass
csidrivers                                 storage.k8s.io/v1                    false      CSIDriver
csinodes                                   storage.k8s.io/v1                    false      CSINode
csistoragecapacities                       storage.k8s.io/v1beta1               true       CSIStorageCapacity
storageclasses                  sc         storage.k8s.io/v1                    false      StorageClass
volumeattachments                          storage.k8s.io/v1                    false      VolumeAttachment

[centos@kubeadm01 ~]$ kubectl explain Pod
KIND:     Pod
VERSION:  v1

DESCRIPTION:
     Pod is a collection of containers that can run on a host. This resource is
     created by clients and scheduled onto hosts.

FIELDS:
   apiVersion <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec <Object>
     Specification of the desired behavior of the pod. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

   status <Object>
     Most recently observed status of the pod. This data may not be up to date.
     Populated by the system. Read-only. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status
```

### Get cluster info

```
[centos@kubeadm01 ~]$ kubectl cluster-info
Kubernetes control plane is running at https://172.31.33.180:6443
CoreDNS is running at https://172.31.33.180:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

[centos@kubeadm01 ~]$ sudo cp -rpa /etc/kubernetes/manifests /etc/kubernetes/manifests.orig
[centos@kubeadm01 ~]$ sudo sed -i '/port=0/d' manifests/kube-scheduler.yaml
[centos@kubeadm01 ~]$ sudo sed -i '/port=0/d' manifests/kube-controller-manager.yaml

[centos@kubeadm01 ~]$ sudo systemctl restart kubelet

[centos@kubeadm01 ~]$ kubectl get cs
Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS    MESSAGE                         ERROR
scheduler            Healthy   ok                              
controller-manager   Healthy   ok                              
etcd-0               Healthy   {"health":"true","reason":""}

[centos@kubeadm01 ~]$ kubectl get nodes
NAME        STATUS   ROLES                  AGE     VERSION
kubeadm01   Ready    control-plane,master   3d10h   v1.22.1
kubeadm02   Ready    <none>                 3d10h   v1.22.1
kubeadm03   Ready    <none>                 3d10h   v1.22.1

[centos@kubeadm01 ~]$ kubectl get nodes -o wide
NAME        STATUS   ROLES                  AGE     VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
kubeadm01   Ready    control-plane,master   3d10h   v1.22.1   172.31.33.180   <none>        CentOS Linux 7 (Core)   5.4.159-1.el7.elrepo.x86_64   docker://20.10.10
kubeadm02   Ready    <none>                 3d10h   v1.22.1   172.31.47.104   <none>        CentOS Linux 7 (Core)   5.4.159-1.el7.elrepo.x86_64   docker://20.10.10
kubeadm03   Ready    <none>                 3d10h   v1.22.1   172.31.38.119   <none>        CentOS Linux 7 (Core)   5.4.159-1.el7.elrepo.x86_64   docker://20.10.10
```

### kubectl context and configuration

```
[centos@kubeadm01 ~]$ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://172.31.33.180:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED

[centos@kubeadm01 ~]$ kubectl config view -o jsonpath='{.users[?(@.name == "kubernetes-admin")]}'
{"name":"kubernetes-admin","user":{"client-certificate-data":"REDACTED","client-key-data":"REDACTED"}}

[centos@kubeadm01 ~]$ kubectl config view -o jsonpath='{.users[?(@.name == "kubernetes-admin")].user.client-key-data}'
REDACTED

[centos@kubeadm01 ~]$ kubectl config view -o jsonpath='{.users[*].name}'
eks_sandbox-eks-cluster-1 kubernetes-admin

[centos@kubeadm01 ~]$ kubectl config get-contexts
CURRENT   NAME         CLUSTER                     AUTHINFO                    NAMESPACE
*         cka          kubernetes                  kubernetes-admin            
          eks-dremio   eks_sandbox-eks-cluster-1   eks_sandbox-eks-cluster-1

[centos@kubeadm01 ~]$ kubectl config current-context
kubernetes-admin@kubernetes

[centos@kubeadm01 ~]$ kubectl config use-context cka
Switched to context "cka".
```

### Node

```
[centos@kubeadm01 ~]$ kubectl describe node kubeadm01
Name:               kubeadm01
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=kubeadm01
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"7e:67:51:c5:6c:3b"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.31.33.180
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sat, 13 Nov 2021 17:19:27 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  kubeadm01
  AcquireTime:     <unset>
  RenewTime:       Wed, 17 Nov 2021 06:31:35 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Sat, 13 Nov 2021 17:25:29 +0000   Sat, 13 Nov 2021 17:25:29 +0000   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Wed, 17 Nov 2021 06:30:50 +0000   Sat, 13 Nov 2021 17:19:24 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Wed, 17 Nov 2021 06:30:50 +0000   Sat, 13 Nov 2021 17:19:24 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Wed, 17 Nov 2021 06:30:50 +0000   Sat, 13 Nov 2021 17:19:24 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Wed, 17 Nov 2021 06:30:50 +0000   Wed, 17 Nov 2021 03:34:47 +0000   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  172.31.33.180
  Hostname:    kubeadm01
Capacity:
  cpu:                2
  ephemeral-storage:  31445996Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             3971396Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  28980629866
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             3868996Ki
  pods:               110
System Info:
  Machine ID:                 05cb8c7b39fe0f70e3ce97e5beab809d
  System UUID:                ec2bbb38-ed07-7d62-3448-bbc340313fc5
  Boot ID:                    33272518-8c49-468c-a16d-b99a77194cd9
  Kernel Version:             5.4.159-1.el7.elrepo.x86_64
  OS Image:                   CentOS Linux 7 (Core)
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.10
  Kubelet Version:            v1.22.1
  Kube-Proxy Version:         v1.22.1
PodCIDR:                      10.192.0.0/24
PodCIDRs:                     10.192.0.0/24
Non-terminated Pods:          (8 in total)
  Namespace                   Name                                 CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                 ------------  ----------  ---------------  -------------  ---
  kube-system                 coredns-78fcd69978-99vcx             100m (5%)     0 (0%)      70Mi (1%)        170Mi (4%)     3d13h
  kube-system                 coredns-78fcd69978-lm5bt             100m (5%)     0 (0%)      70Mi (1%)        170Mi (4%)     3d13h
  kube-system                 etcd-kubeadm01                       100m (5%)     0 (0%)      100Mi (2%)       0 (0%)         3d13h
  kube-system                 kube-apiserver-kubeadm01             250m (12%)    0 (0%)      0 (0%)           0 (0%)         3d13h
  kube-system                 kube-controller-manager-kubeadm01    200m (10%)    0 (0%)      0 (0%)           0 (0%)         177m
  kube-system                 kube-flannel-ds-b6c49                100m (5%)     100m (5%)   50Mi (1%)        50Mi (1%)      3d13h
  kube-system                 kube-proxy-7r755                     0 (0%)        0 (0%)      0 (0%)           0 (0%)         3d13h
  kube-system                 kube-scheduler-kubeadm01             100m (5%)     0 (0%)      0 (0%)           0 (0%)         176m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                950m (47%)  100m (5%)
  memory             290Mi (7%)  390Mi (10%)
  ephemeral-storage  0 (0%)      0 (0%)
  hugepages-1Gi      0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:              <none>

[centos@kubeadm01 ~]$ kubectl label node kubeadm01 disktype=ssd
node/kubeadm01 labeled

# update a label
[centos@kubeadm01 ~]$ kubectl label node kubeadm01 disktype=nvme --overwrite
node/kubeadm01 labeled

[centos@kubeadm01 ~]$ kubectl  get node -l "disktype=ssd"
No resources found
[centos@kubeadm01 ~]$ kubectl  get node -l "disktype=nvme"
NAME        STATUS   ROLES                  AGE     VERSION
kubeadm01   Ready    control-plane,master   3d13h   v1.22.1

# delete a label
[centos@kubeadm01 ~]$ kubectl label node kubeadm01 disktype-
node/kubeadm01 labeled

# Schedule a Pod to a specific Node
[centos@kubeadm01 ~]$ kubectl label node kubeadm02 disktype=ssd
node/kubeadm02 labeled

[centos@kubeadm01 ~]$ mkdir cka
[centos@kubeadm01 ~]$ cd cka
[centos@kubeadm01 cka]$ kubectl run nginx --image=nginx --image-pull-policy=IfNotPresent --dry-run=client -o yaml > pod-nodeSelector.yaml
[centos@kubeadm01 cka]$ vim pod-nodeSelector.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
  nodeSelector:
    disktype: ssd
    
[centos@kubeadm01 cka]$ kubectl apply -f pod-nodeSelector.yaml 
pod/nginx created

[centos@kubeadm01 cka]$ kubectl get pods -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE        NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          23s   10.192.1.3   kubeadm02   <none>           <none>

# Taint a Node
[centos@kubeadm01 cka]$ kubectl taint node kubeadm02 notService=nginx:NoSchedule
node/kubeadm02 tainted
[centos@kubeadm01 cka]$ kubectl taint node kubeadm02 notService=nginx:NoExecute
node/kubeadm02 tainted

[centos@kubeadm01 cka]$ kubectl describe node kubeadm02 | grep NoSchedule
                    notService=nginx:NoSchedule
[centos@kubeadm01 cka]$ kubectl describe node kubeadm02 | grep NoExecute
Taints:             notService=nginx:NoExecut

[centos@kubeadm01 cka]$ kubectl get pods -o wide
No resources found in default namespace.

[centos@kubeadm01 cka]$ kubectl taint node kubeadm02 notService:NoSchedule-
node/kubeadm02 untainted
[centos@kubeadm01 cka]$ kubectl taint node kubeadm02 notService:NoExecute-
node/kubeadm02 untainted

[centos@kubeadm01 cka]$ kubectl describe node kubeadm02 | grep Taints
Taints:             <none>

# Node administration
[centos@kubeadm01 cka]$ kubectl cordon kubeadm02
node/kubeadm02 cordoned

[centos@kubeadm01 cka]$ kubectl get nodes
NAME        STATUS                     ROLES                  AGE     VERSION
kubeadm01   Ready                      control-plane,master   3d16h   v1.22.1
kubeadm02   Ready,SchedulingDisabled   <none>                 3d15h   v1.22.1
kubeadm03   Ready                      <none>                 3d15h   v1.22.1
  
[centos@kubeadm01 cka]$ kubectl drain kubeadm02 --ignore-daemonsets
node/kubeadm02 already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-flannel-ds-nspjx, kube-system/kube-proxy-nxz8c
node/kubeadm02 drained

[centos@kubeadm01 cka]$ kubectl describe node kubeadm02 | grep -i schedulable
Taints:             node.kubernetes.io/unschedulable:NoSchedule
Unschedulable:      true
  Normal  NodeSchedulable     2m17s                kubelet  Node kubeadm02 status is now: NodeSchedulable
  Normal  NodeNotSchedulable  97s (x2 over 7m50s)  kubelet  Node kubeadm02 status is now: NodeNotSchedulable

[centos@kubeadm01 cka]$ kubectl uncordon kubeadm02
node/kubeadm02 uncordoned

[centos@kubeadm01 cka]$ kubectl get nodes
NAME        STATUS   ROLES                  AGE     VERSION
kubeadm01   Ready    control-plane,master   3d16h   v1.22.1
kubeadm02   Ready    <none>                 3d15h   v1.22.1
kubeadm03   Ready    <none>                 3d15h   v1.22.1

[centos@kubeadm01 cka]$ kubectl describe node kubeadm02 | grep -i schedulable
Unschedulable:      false
  Normal  NodeNotSchedulable  5m38s  kubelet  Node kubeadm02 status is now: NodeNotSchedulable
  Normal  NodeSchedulable     5s     kubelet  Node kubeadm02 status is now: NodeSchedulable
```

### ConfigMap

```
[centos@kubeadm01 cka]$ kubectl create configmap cm-mytest --from-literal=name=damondguo --from-literal=sex=male
configmap/cm-mytest created

[centos@kubeadm01 cka]$ kubectl get configmap
NAME               DATA   AGE
cm-mytest          2      18s
kube-root-ca.crt   1      3d16h

[centos@kubeadm01 cka]$ kubectl describe configmap cm-mytest
Name:         cm-mytest
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
name:
----
damondguo
sex:
----
male

BinaryData
====

Events:  <none>

[centos@kubeadm01 cka]$ kubectl get configmap cm-mytest -o yaml
apiVersion: v1
data:
  name: damondguo
  sex: male
kind: ConfigMap
metadata:
  creationTimestamp: "2021-11-17T09:36:11Z"
  name: cm-mytest
  namespace: default
  resourceVersion: "445866"
  uid: 12a091d2-8288-40d6-aabc-35080eed3646
  
[centos@kubeadm01 cka]$ kubectl create configmap cm-mytest --from-literal=name=tina --from-literal=sex=female --dry-run=client -o yaml > cm-mytest.yaml
[centos@kubeadm01 cka]$ vim cm-mytest.yaml
---
apiVersion: v1
data:
  name: tina
  sex: female
kind: ConfigMap
metadata:
  name: cm-mytest

[centos@kubeadm01 cka]$ kubectl replace -f cm-mytest.yaml 
configmap/cm-mytest replaced

[centos@kubeadm01 cka]$ kubectl get configmap cm-mytest -o yaml
apiVersion: v1
data:
  name: tina
  sex: female
kind: ConfigMap
metadata:
  creationTimestamp: "2021-11-17T09:36:11Z"
  name: cm-mytest
  namespace: default
  resourceVersion: "446411"
  uid: 12a091d2-8288-40d6-aabc-35080eed3646

[centos@kubeadm01 cka]$ cat > db.properties <<EOF
driverClassName=com.mysql.jdbc.Driver
dbname=db-name-dev
dbpassword=db-password
EOF

[centos@kubeadm01 cka]$ kubectl create configmap db-conf --from-file=db.properties
configmap/db-conf created

[centos@kubeadm01 cka]$ kubectl get configmap db-conf -o yaml
apiVersion: v1
data:
  db.properties: |
    driverClassName=com.mysql.jdbc.Driver
    dbname=db-name-dev
    dbpassword=db-password
kind: ConfigMap
metadata:
  creationTimestamp: "2021-11-17T09:51:09Z"
  name: db-conf
  namespace: default
  resourceVersion: "447130"
  uid: b6ba03f8-51ef-48ba-9c43-9b7ab07294d1
  
[centos@kubeadm01 cka]$ mkdir conf-dir
[centos@kubeadm01 cka]$ cat > conf-dir/a.conf <<EOF
a.username=a-name
a.password=a-pass
EOF
[centos@kubeadm01 cka]$ cat > conf-dir/b.conf <<EOF
b.username=b-name
b.password=b-pass
EOF

[centos@kubeadm01 cka]$ kubectl create configmap conf-dir --from-file=conf-dir/
configmap/conf-dir created

[centos@kubeadm01 cka]$ kubectl get configmap conf-dir -o yaml
apiVersion: v1
data:
  a.conf: |
    a.username=a-name
    a.password=a-pass
  b.conf: |
    b.username=b-name
    b.password=b-pass
kind: ConfigMap
metadata:
  creationTimestamp: "2021-11-18T08:47:22Z"
  name: conf-dir
  namespace: default
  resourceVersion: "562767"
  uid: 20079f9f-98e3-48f0-9b19-b42fefdc21a8

# Get Configmap value in Pod

[centos@kubeadm01 cka]$ cat > get-cm-pod.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: cm-test-pod
spec:
  containers:
  - name: cm-test
    image: busybox
    command: ["/bin/sh", "-c", "env | grep MYTEST"]
    env:
    - name: MYTEST-NAME
      valueFrom:
        configMapKeyRef:
          name: cm-mytest
          key: name
    - name: MYTEST-SEX
      valueFrom:
        configMapKeyRef:
          name: cm-mytest
          key: sex
  restartPolicy: Never
EOF

[centos@kubeadm01 cka]$ kubectl apply -f get-cm-pod.yaml 
pod/cm-test-pod created

[centos@kubeadm01 cka]$ kubectl get pods 
NAME          READY   STATUS      RESTARTS   AGE
cm-test-pod   0/1     Completed   0          17s

[centos@kubeadm01 cka]$ kubectl logs cm-test-pod
MYTEST-SEX=female
MYTEST-NAME=tina


[centos@kubeadm01 cka]$ cat > cm-mount-pod.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: cm-conf-dir-test-pod
spec:
  containers:
    - name: cm-conf-dir-test
      image: busybox
      command: [ "/bin/sh", "-c", "grep a -r /etc/config/" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: conf-dir
        items: 
        - key: a.conf
          path: a.conf
  restartPolicy: Never
EOF

[centos@kubeadm01 cka]$ kubectl apply -f cm-mount-pod.yaml 
pod/cm-conf-dir-test-pod created

[centos@kubeadm01 cka]$ kubectl get pods
NAME                   READY   STATUS      RESTARTS   AGE
cm-conf-dir-test-pod   0/1     Completed   0          18s
cm-test-pod            0/1     Completed   0          42m

[centos@kubeadm01 cka]$ kubectl logs cm-conf-dir-test-pod
/etc/config/..2021_11_18_09_46_44.591729779/a.conf:a.username=a-name
/etc/config/..2021_11_18_09_46_44.591729779/a.conf:a.password=a-pass
/etc/config/a.conf:a.username=a-name
/etc/config/a.conf:a.password=a-pass
```

### Secret

```
[centos@kubeadm01 cka]$ echo "kit" > username.txt
[centos@kubeadm01 cka]$ echo "boy616" > password.txt
[centos@kubeadm01 cka]$ kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
secret/db-user-pass created
[centos@kubeadm01 cka]$ kubectl describe secret db-user-pass
Name:         db-user-pass
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password.txt:  7 bytes
username.txt:  4 bytes

[centos@kubeadm01 cka]$ kubectl create secret generic db-user-secret --from-literal=username='kit' --from-literal=password='boy616'
secret/db-user-secret created
[centos@kubeadm01 cka]$ kubectl describe secret db-user-secret
Name:         db-user-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  6 bytes
username:  3 bytes

[centos@kubeadm01 cka]$ echo -n "kit" > username.txt
[centos@kubeadm01 cka]$ echo -n "boy616" > password.txt
[centos@kubeadm01 cka]$ kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt -o yaml --dry-run=client | kubectl replace -f -
secret/db-user-pass replaced
[centos@kubeadm01 cka]$ kubectl describe secret db-user-pass
Name:         db-user-pass
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password.txt:  6 bytes
username.txt:  3 bytes

[centos@kubeadm01 cka]$ kubectl get secret db-user-pass -o yaml
apiVersion: v1
data:
  password.txt: Ym95NjE2
  username.txt: a2l0
kind: Secret
metadata:
  creationTimestamp: "2021-11-19T05:58:15Z"
  name: db-user-pass
  namespace: default
  resourceVersion: "670722"
  uid: 1beff122-2387-4ca1-874e-f86fad81bca7
type: Opaque

[centos@kubeadm01 cka]$ kubectl get secret db-user-secret -o yaml
apiVersion: v1
data:
  password: Ym95NjE2
  username: a2l0
kind: Secret
metadata:
  creationTimestamp: "2021-11-19T06:00:26Z"
  name: db-user-secret
  namespace: default
  resourceVersion: "669765"
  uid: bba44dde-7c93-4cce-a900-f6980189613d
type: Opaque

[centos@kubeadm01 cka]$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
db-user-pass          Opaque                                2      4m22s
db-user-secret        Opaque                                2      2m11s
default-token-x6s6c   kubernetes.io/service-account-token   3      5d12h

[centos@kubeadm01 cka]$ echo -n 'kit' | base64 
a2l0

[centos@kubeadm01 cka]$ echo -n 'a2l0' | base64 -d
kit

[centos@kubeadm01 cka]$ echo -n "boy616" | base64
Ym95NjE2

[centos@kubeadm01 cka]$ cat > key-value-type-secret.yaml <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: kv-secret
type: Opaque
data:
  username: a2l0
  password: Ym95NjE2
EOF
[centos@kubeadm01 cka]$ kubectl apply -f key-value-type-secret.yaml 
secret/kv-secret created
[centos@kubeadm01 cka]$ kubectl get secret kv-secret -o yaml 
apiVersion: v1
data:
  password: Ym95NjE2
  username: a2l0
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"password":"Ym95NjE2","username":"a2l0"},"kind":"Secret","metadata":{"annotations":{},"name":"kv-secret","namespace":"default"},"type":"Opaque"}
  creationTimestamp: "2021-11-19T06:23:03Z"
  name: kv-secret
  namespace: default
  resourceVersion: "671666"
  uid: 6ca18c9d-ed28-402a-94de-57694747cff4
type: Opaque

[centos@kubeadm01 cka]$ kubectl edit secrets kv-secret
:q

Edit cancelled, no changes made.

[centos@kubeadm01 cka]$ cat > file-type-secret.yaml <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: file-secret
type: Opaque
stringData:
  config.yaml: |-
    apiUrl: "https://my.api.com/api/v1"
    username: {{username}}
    password: {{password}}
EOF

[centos@kubeadm01 cka]$ kubectl apply -f file-type-secret.yaml
secret/file-secret created

[centos@kubeadm01 cka]$ kubectl get secret file-secret -o yaml
apiVersion: v1
data:
  config.yaml: YXBpVXJsOiAiaHR0cHM6Ly9teS5hcGkuY29tL2FwaS92MSIKdXNlcm5hbWU6IHt7dXNlcm5hbWV9fQpwYXNzd29yZDoge3twYXNzd29yZH19
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Secret","metadata":{"annotations":{},"name":"file-secret","namespace":"default"},"stringData":{"config.yaml":"apiUrl: \"https://my.api.com/api/v1\"\nusername: {{username}}\npassword: {{password}}"},"type":"Opaque"}
  creationTimestamp: "2021-11-19T06:58:38Z"
  name: file-secret
  namespace: default
  resourceVersion: "674655"
  uid: 8e9b9a42-9747-4643-b965-ce96311ab584
type: Opaque

[centos@kubeadm01 cka]$ kubectl describe secret file-secret
Name:         file-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
config.yaml:  81 bytes

[centos@kubeadm01 cka]$ cat > mysecret.yaml <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: a2l0
  password: Ym95NjE2
stringData:
  username: tina
EOF

[centos@kubeadm01 cka]$ kubectl apply -f mysecret.yaml 
secret/mysecret created
[centos@kubeadm01 cka]$ kubectl get secret mysecret -o yaml
apiVersion: v1
data:
  password: Ym95NjE2
  username: dGluYQ==
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"password":"Ym95NjE2","username":"a2l0"},"kind":"Secret","metadata":{"annotations":{},"name":"mysecret","namespace":"default"},"stringData":{"username":"tina"},"type":"Opaque"}
  creationTimestamp: "2021-11-19T07:06:25Z"
  name: mysecret
  namespace: default
  resourceVersion: "675751"
  uid: 24cd3b70-2412-4800-97cf-bfd2dcb73048
type: Opaque

[centos@kubeadm01 cka]$ echo 'dGluYQ==' | base64 -d
tina

cat > secret-var.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx
    image: nginx
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
EOF

[centos@kubeadm01 cka]$ kubectl create -f secret-var.yaml 
pod/mypod created

[centos@kubeadm01 cka]$ kubectl get pods
NAME                   READY   STATUS      RESTARTS   AGE
cm-conf-dir-test-pod   0/1     Completed   0          21h
cm-test-pod            0/1     Completed   0          22h
mypod                  1/1     Running     0          21

[centos@kubeadm01 cka]$ kubectl exec -it mypod -- /bin/bash
root@mypod:/# echo $SECRET_USERNAME
tina
root@mypod:/# echo $SECRET_PASSWORD
boy616
root@mypod:/# exit
exit

[centos@kubeadm01 cka]$ kubectl delete -f secret-var.yaml
pod "mypod" deleted

[centos@kubeadm01 cka]$ cat > secret-vol.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
EOF

[centos@kubeadm01 cka]$ kubectl create -f secret-vol.yaml
pod/mypod created

[centos@kubeadm01 cka]$ kubectl exec -i mypod -- ls /etc/foo/
password
username
[centos@kubeadm01 cka]$ kubectl exec -i mypod -- cat /etc/foo/username
tina
[centos@kubeadm01 cka]$ kubectl exec -i mypod -- cat /etc/foo/password
boy616
```

### Examples

```
# Job and CronJob
[centos@kubeadm01 ~]$ kubectl create job hello --image=busybox -- echo "Hello World Job"
job.batch/hello created
[centos@kubeadm01 ~]$ kubectl get jobs
NAME    COMPLETIONS   DURATION   AGE
hello   1/1           3s         4s
[centos@kubeadm01 ~]$ kubectl logs job/hello
Hello World Job

[centos@kubeadm01 ~]$ kubectl create cronjob hello-cron --image=busybox   --schedule="*/1 * * * *" -- echo "Hello World Cron Job" 
cronjob.batch/hello-cron created
[centos@kubeadm01 ~]$ kubectl get cronjobs
NAME         SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
hello-cron   */1 * * * *   False     0        <none>          5s
[centos@kubeadm01 ~]$ kubectl get jobs
NAME                  COMPLETIONS   DURATION   AGE
hello                 1/1           4s         61s
hello-cron-27289580   0/1           0s         0s

[centos@kubeadm01 ~]$ kubectl delete cronjob/hello-cron
cronjob.batch "hello-cron" deleted
[centos@kubeadm01 ~]$ kubectl get jobs
NAME    COMPLETIONS   DURATION   AGE
hello   1/1           4s         2m7s

[centos@kubeadm01 ~]$ kubectl delete job/hello
job.batch "hello" deleted
[centos@kubeadm01 ~]$ kubectl get jobs
No resources found in default namespace.

# Create multiple YAML objects from stdin
[centos@kubeadm01 cka]$ cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: busybox-sleep
spec:
  containers:
  - name: busybox
    image: busybox
    args:
    - sleep
    - "1000000"
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox-sleep-less
spec:
  containers:
  - name: busybox
    image: busybox
    args:
    - sleep
    - "1000"
EOF
pod/busybox-sleep created
pod/busybox-sleep-less created

[centos@kubeadm01 cka]$ kubectl get pods
NAME                   READY   STATUS      RESTARTS   AGE
busybox-sleep          1/1     Running     0          64s
busybox-sleep-less     1/1     Running     0          64s

# Create a secret with several keys
[centos@kubeadm01 cka]$ cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: $(echo -n "s33msi4" | base64 -w0)
  username: $(echo -n "jane" | base64 -w0)
EOF
secret/mysecret configured
[centos@kubeadm01 cka]$ kubectl get secret/mysecret -o yaml
apiVersion: v1
data:
  password: czMzbXNpNA==
  username: amFuZQ==
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"password":"czMzbXNpNA==","username":"amFuZQ=="},"kind":"Secret","metadata":{"annotations":{},"name":"mysecret","namespace":"default"},"type":"Opaque"}
  creationTimestamp: "2021-11-19T07:06:25Z"
  name: mysecret
  namespace: default
  resourceVersion: "773060"
  uid: 24cd3b70-2412-4800-97cf-bfd2dcb73048
type: Opaque

# Describe commands with verbose output
[centos@kubeadm01 cka]$ kubectl describe pod/busybox-sleep
Name:         busybox-sleep
Namespace:    default
Priority:     0
Node:         kubeadm02/172.31.47.104
Start Time:   Sat, 20 Nov 2021 02:23:02 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.192.1.18
IPs:
  IP:  10.192.1.18
Containers:
  busybox:
    Container ID:  docker://5f99c1c163b31f2f046a4ae1aef02df3811d4a06e8e848d08f5a1ad8204d4cc0
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:e7157b6d7ebbe2cce5eaa8cfe8aa4fa82d173999b9f90a9ec42e57323546c353
    Port:          <none>
    Host Port:     <none>
    Args:
      sleep
      1000000
    State:          Running
      Started:      Sat, 20 Nov 2021 02:23:06 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vvjlv (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-vvjlv:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  7m44s  default-scheduler  Successfully assigned default/busybox-sleep to kubeadm02
  Normal  Pulling    7m44s  kubelet            Pulling image "busybox"
  Normal  Pulled     7m41s  kubelet            Successfully pulled image "busybox" in 2.409482032s
  Normal  Created    7m41s  kubelet            Created container busybox
  Normal  Started    7m41s  kubelet            Started container busybox
 
# Get all worker nodes (use a selector to exclude results that have a label
# named 'node-role.kubernetes.io/master')
[centos@kubeadm01 cka]$ kubectl get node --selector='!node-role.kubernetes.io/master'
NAME        STATUS   ROLES    AGE    VERSION
kubeadm02   Ready    <none>   6d9h   v1.22.1
kubeadm03   Ready    <none>   6d9h   v1.22.1

# Get all running pods in the namespace
[centos@kubeadm01 cka]$ kubectl get pods
NAME                   READY   STATUS      RESTARTS   AGE
busybox-sleep          1/1     Running     0          10m
busybox-sleep-less     1/1     Running     0          10m
cm-conf-dir-test-pod   0/1     Completed   0          40h
cm-test-pod            0/1     Completed   0          41h
mypod                  1/1     Running     0          19h
[centos@kubeadm01 cka]$ kubectl get pods --field-selector=status.phase=Running
NAME                 READY   STATUS    RESTARTS   AGE
busybox-sleep        1/1     Running   0          10m
busybox-sleep-less   1/1     Running   0          10m
mypod                1/1     Running   0          19h

# Compares the current state of the cluster against the state that the cluster would be in if the manifest was applied.
[centos@kubeadm01 cka]$ cat cm-mytest.yaml 
apiVersion: v1
data:
  name: tina
  sex: female
kind: ConfigMap
metadata:
  name: cm-mytest
[centos@kubeadm01 cka]$ sed -i s/tina/lily/g cm-mytest.yaml 
[centos@kubeadm01 cka]$ kubectl diff -f ./cm-mytest.yaml 
diff -u -N /tmp/LIVE-527004765/v1.ConfigMap.default.cm-mytest /tmp/MERGED-909896216/v1.ConfigMap.default.cm-mytest
--- /tmp/LIVE-527004765/v1.ConfigMap.default.cm-mytest  2021-11-20 02:34:58.258938099 +0000
+++ /tmp/MERGED-909896216/v1.ConfigMap.default.cm-mytest        2021-11-20 02:34:58.259938124 +0000
@@ -1,6 +1,6 @@
 apiVersion: v1
 data:
-  name: tina
+  name: lily
   sex: female
 kind: ConfigMap
 metadata:
@@ -17,11 +17,18 @@
     fieldsType: FieldsV1
     fieldsV1:
       f:data:
-        f:name: {}
         f:sex: {}
     manager: kubectl-replace
     operation: Update
     time: "2021-11-17T09:42:40Z"
+  - apiVersion: v1
+    fieldsType: FieldsV1
+    fieldsV1:
+      f:data:
+        f:name: {}
+    manager: kubectl-client-side-apply
+    operation: Update
+    time: "2021-11-20T02:34:58Z"
   name: cm-mytest
   namespace: default
   resourceVersion: "446411"
```
