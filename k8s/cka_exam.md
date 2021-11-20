## CKA Exam

### ClusterRole and RoleBinding

https://kubernetes.io/zh/docs/reference/access-authn-authz/rbac/#kubectl-create-clusterrole

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

### Drain a Node

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

### Backup and restore etcd

```
sudo apt install etcd-client

ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
--cert=/opt/KUIN00601/etcd-client.crt \
--key=/opt/KUIN00601/etc-client.key \
snapshot save /srv/data/etcd-snapshot.db

ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
--cert=/opt/KUIN00601/etcd-client.crt \
--key=/opt/KUIN00601/etc-client.key \
snapshot restore /var/lib/backup/etcd-snapshot-previous.db
```

### Create NetworkPolicy

https://kubernetes.io/zh/docs/concepts/services-networking/network-policies/#networkpolicy-resource

```
kubectl config use-context hk8s

cat > network-policy.yaml <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: internal
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector: {}
    ports:
    - protocol: TCP
      port: 9000
EOF

kubectl describe ns corp-bar

cat > network-policy.yaml <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: internal
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          project: corp-bar
    ports:
    - protocol: TCP
      port: 5679
EOF

kubectl apply -f network-policy.yaml 
```

```
[centos@kubeadm01 cka]$ kubectl create namespace internal
namespace/internal created

[centos@kubeadm01 cka]$ cat > network-policy.yaml <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: internal
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector: {}
    ports:
    - protocol: TCP
      port: 9000
EOF

[centos@kubeadm01 cka]$ kubectl apply -f network-policy.yaml 
networkpolicy.networking.k8s.io/allow-port-from-namespace created

[centos@kubeadm01 cka]$ kubectl -n internal get networkpolicies
NAME                        POD-SELECTOR   AGE
allow-port-from-namespace   <none>         36s

[centos@kubeadm01 cka]$ kubectl -n internal describe networkpolicy/allow-port-from-namespace
Name:         allow-port-from-namespace
Namespace:    internal
Created on:   2021-11-20 07:52:10 +0000 UTC
Labels:       <none>
Annotations:  <none>
Spec:
  PodSelector:     <none> (Allowing the specific traffic to all pods in this namespace)
  Allowing ingress traffic:
    To Port: 9000/TCP
    From:
      PodSelector: <none>
  Not affecting egress traffic
  Policy Types: Ingress
```

### Create Service

```
kubectl config use-context k8s

kubectl expose deployment front-end --port=80 --target-port=80 --protocol=TCP --type=NodePort --name=front-end-svc

kubectl get svc
```

```
[centos@kubeadm01 cka]$ kubectl expose deployment nginx-deployment --port=80 --target-port=80 --protocol=TCP --type=NodePort --name=nginx-deployment-svc
service/nginx-deployment-svc exposed

[centos@kubeadm01 cka]$ kubectl get svc
NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
kubernetes             ClusterIP   10.254.0.1      <none>        443/TCP                      6d14h
nginx-deployment-svc   NodePort    10.254.131.34   <none>        80:31495/TCP                 6s
```

### Create Ingress

```
```
