## CKA Exam

### ClusterRole

https://kubernetes.io/zh/docs/reference/access-authn-authz/rbac/

```
[centos@kubeadm01 cka]$ kubectl create namespace app-team1
namespace/app-team1 created

[centos@kubeadm01 cka]$ kubectl create clusterrole deployment-clusterrole --verb=create --resource=deployments,statefulsets,daemonsets
clusterrole.rbac.authorization.k8s.io/deployment-clusterrole created

[centos@kubeadm01 cka]$ kubectl -n app-team1 create serviceaccount cicd-token
serviceaccount/cicd-token created

[centos@kubeadm01 cka]$ kubectl -n app-team1 create rolebinding cicd-token-binding --clusterrole=deployment-clusterrole --serviceaccount=app-team1:cicd-token
rolebinding.rbac.authorization.k8s.io/cicd-token-binding created

[centos@kubeadm01 cka]$ kubectl -n app-team1 describe rolebinding.rbac.authorization.k8s.io/cicd-token-binding
Name:         cicd-token-binding
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  deployment-clusterrole
Subjects:
  Kind            Name        Namespace
  ----            ----        ---------
  ServiceAccount  cicd-token  app-team1
```

### Drain

```
kubectl config use-context ek8s

kubectl drain ek8s-node-1 --delete-emptydir-data --ignore-daemonsets --force
```

```
[centos@kubeadm01 cka]$ kubectl get nodes
NAME        STATUS   ROLES                  AGE     VERSION
kubeadm01   Ready    control-plane,master   6d13h   v1.22.1
kubeadm02   Ready    <none>                 6d13h   v1.22.1
kubeadm03   Ready    <none>                 6d13h   v1.22.1

[centos@kubeadm01 cka]$ kubectl cordon kubeadm03 
node/kubeadm03 cordoned

[centos@kubeadm01 cka]$ kubectl drain kubeadm03 --delete-emptydir-data --ignore-daemonsets --force
node/kubeadm03 already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-flannel-ds-sl5l2, kube-system/kube-proxy-fm5nw
evicting pod kubernetes-dashboard/kubernetes-dashboard-576cb95f94-2n6dr
evicting pod default/nginx-deployment-5d47ff8589-stffs
evicting pod default/nginx-deployment-5d47ff8589-tzggx
evicting pod kubernetes-dashboard/dashboard-metrics-scraper-c45b7869d-cdp4m
pod/dashboard-metrics-scraper-c45b7869d-cdp4m evicted
pod/nginx-deployment-5d47ff8589-stffs evicted
pod/kubernetes-dashboard-576cb95f94-2n6dr evicted
pod/nginx-deployment-5d47ff8589-tzggx evicted
node/kubeadm03 evicted

[centos@kubeadm01 cka]$ kubectl get nodes
NAME        STATUS                     ROLES                  AGE     VERSION
kubeadm01   Ready                      control-plane,master   6d13h   v1.22.1
kubeadm02   Ready                      <none>                 6d13h   v1.22.1
kubeadm03   Ready,SchedulingDisabled   <none>                 6d13h   v1.22.1

[centos@kubeadm01 cka]$ kubectl uncordon kubeadm03
node/kubeadm03 uncordoned
[centos@kubeadm01 cka]$ kubectl get nodes
NAME        STATUS   ROLES                  AGE     VERSION
kubeadm01   Ready    control-plane,master   6d13h   v1.22.1
kubeadm02   Ready    <none>                 6d13h   v1.22.1
kubeadm03   Ready    <none>                 6d13h   v1.22.1
```

### Upgrade Master Node

```
kubectl config use-context mk8s

kubectl get nodes
kubectl drain mk8s-master-1 --delete-emptydir-data --ignore-daemonsets --force

ssh mk8s-master-1
sudo -i
apt-get install -y kubeadm=1.20.1-00
kubeadm version

kubeadm upgrade plan
kubeadm upgrade apply v1.20.1 --etcd-upgrade=false

apt-get install kubelet=1.20.1-00 kubectl=1.20.1-00

kubelet version
kubectl version

systemctl status kubelet
systemctl daemon-reload

exit
exit
```
