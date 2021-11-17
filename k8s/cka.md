## CKA Notes

### kubectl auto completion

```bash
sudo yum install -y bash-completion

cat >> ~/.bashrc <<EOF
# kubectl auto completion
source <(kubectl completion bash)
complete -F __start_kubectl kubectl
EOF

source ~/.bashrc
```

### Kubernetes resources

```bash
kubectl api-resources
```

```
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
```

```bash
kubectl explain Pod
```

```
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

```bash
kubectl cluster-info
```

```
Kubernetes control plane is running at https://172.31.33.180:6443
CoreDNS is running at https://172.31.33.180:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

```bash
ssh root@kubeadm01

cd /etc/kubernetes/
cp -rpa manifests manifests.orig
sed -i '/port=0/d' manifests/kube-scheduler.yaml
sed -i '/port=0/d' manifests/kube-controller-manager.yaml

systemctl restart kubelet

kubectl get cs
```

```
Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS    MESSAGE                         ERROR
scheduler            Healthy   ok                              
controller-manager   Healthy   ok                              
etcd-0               Healthy   {"health":"true","reason":""}
```

```bash
kubectl get nodes
```

```
NAME        STATUS   ROLES                  AGE     VERSION
kubeadm01   Ready    control-plane,master   3d10h   v1.22.1
kubeadm02   Ready    <none>                 3d10h   v1.22.1
kubeadm03   Ready    <none>                 3d10h   v1.22.1
```

```bash
kubectl get nodes -o wide
```

```
NAME        STATUS   ROLES                  AGE     VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
kubeadm01   Ready    control-plane,master   3d10h   v1.22.1   172.31.33.180   <none>        CentOS Linux 7 (Core)   5.4.159-1.el7.elrepo.x86_64   docker://20.10.10
kubeadm02   Ready    <none>                 3d10h   v1.22.1   172.31.47.104   <none>        CentOS Linux 7 (Core)   5.4.159-1.el7.elrepo.x86_64   docker://20.10.10
kubeadm03   Ready    <none>                 3d10h   v1.22.1   172.31.38.119   <none>        CentOS Linux 7 (Core)   5.4.159-1.el7.elrepo.x86_64   docker://20.10.10
```


