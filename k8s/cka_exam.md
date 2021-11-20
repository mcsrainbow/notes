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

https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

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

https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource

```
cat > pong-ingress.yaml <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: Pong
  namespace: ing-internal
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /hi
        pathType: Prefix
        backend:
          service:
            name: hi
            port:
              number: 5678
EOF

kubectl -f pong-ingress.yaml

kubectl -n ing-internal get pods -o wide

curl -kL $ingress-ip
hi
```

### Scale Deployment

```
kubectl scale deployment webserver --replicas=6
```

### nodeSelector

```
kubectl run nginx-kusc00401 --image=nginx --dry-run=client -o yaml > pod-nginx.yaml

vi pod-nginx.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: spinning

kubectl apply -f pod-nginx.yaml
kubectl get pod nginx-kusc00401 -o wide
```

```
[centos@kubeadm01 cka]$ kubectl run nginx-kusc00401 --image=nginx --dry-run=client -o yaml > pod-nginx.yaml
[centos@kubeadm01 cka]$ cat pod-nginx.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-kusc00401
  name: nginx-kusc00401
spec:
  containers:
  - image: nginx
    name: nginx-kusc00401
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

[centos@kubeadm01 cka]$ vim pod-nginx.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: spinning

[centos@kubeadm01 cka]$ kubectl apply -f pod-nginx.yaml 
pod/nginx-kusc00401 created

[centos@kubeadm01 cka]$ kubectl get pod nginx-kusc00401 -o wide
NAME              READY   STATUS    RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
nginx-kusc00401   0/1     Pending   0          33s   <none>   <none>   <none>           <none>

[centos@kubeadm01 cka]$ kubectl label node kubeadm03 disk=spinning
node/kubeadm03 labeled

[centos@kubeadm01 cka]$ kubectl get pod nginx-kusc00401 -o wide
NAME              READY   STATUS    RESTARTS   AGE    IP            NODE        NOMINATED NODE   READINESS GATES
nginx-kusc00401   1/1     Running   0          101s   10.192.2.41   kubeadm03   <none>           <none>
```

### Node Health

```
kubectl describe node

kubectl describe node | grep -i taints | grep -v -i noschedule

echo 2 > /opt/KUSC00402/kusc00402.txt
```

```
[centos@kubeadm01 cka]$ kubectl describe node | grep -i taints
Taints:             node-role.kubernetes.io/master:NoSchedule
Taints:             <none>
Taints:             <none>
```

### Create a Pod with multiple containers

```
kubectl run kucc1 --image=nginx --dry-run=client -o yaml > pod-kucc1.yaml

vim pod-kucc1.yaml 
---
apiVersion: v1
kind: Pod
metadata:
  name: kucc1
spec:
  containers:
  - name: nginx
    image: nginx
  - name: redis
    image: redis
  - name: memcached
    image: memcached
  - name: consul
    image: consul
    
kubectl apply -f pod-kucc1.yaml 

kubectl get pods | grep kucc1
```

```
[centos@kubeadm01 cka]$ kubectl run kucc1 --image=nginx --dry-run=client -o yaml > pod-kucc1.yaml
[centos@kubeadm01 cka]$ cat pod-kucc1.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: kucc1
  name: kucc1
spec:
  containers:
  - image: nginx
    name: kucc1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
[centos@kubeadm01 cka]$ vim pod-kucc1.yaml 
[centos@kubeadm01 cka]$ cat pod-kucc1.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: kucc1
spec:
  containers:
  - name: nginx
    image: nginx
  - name: redis
    image: redis
  - name: memcached
    image: memcached
  - name: consul
    image: consul

[centos@kubeadm01 cka]$ kubectl apply -f pod-kucc1.yaml 
pod/kucc1 created
[centos@kubeadm01 cka]$ kubectl get pods | grep kucc1
kucc1                               4/4     Running     0                32s
```

### Create Persistent Volume

https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolume

```
cat > app-config-pv.yaml <<EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/srv/app-config"
EOF

kubectl apply -f app-data-pv.yaml

kubectl get pv
```
```
[centos@kubeadm01 cka]$ sudo mkdir /srv/app-config
[centos@kubeadm02 ~]$ sudo mkdir /srv/app-config
[centos@kubeadm03 ~]$ sudo mkdir /srv/app-config
[centos@kubeadm01 ~]$ cat > app-config-pv.yaml <<EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/srv/app-config"
EOF

[centos@kubeadm01 cka]$ kubectl apply -f app-config-pv.yaml 
persistentvolume/app-config created
[centos@kubeadm01 cka]$ kubectl get pv
NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
app-config   2Gi        RWX            Retain           Available                                   9s
```

### Create Persistent Volume Claim

https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolumeclaim

```
cat > pv-volume-pvc.yaml <<EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-volume
spec:
  storageClassName: csi-hostpath-sc
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
---
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: pv-volume
  containers:
    - name: web-server
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
EOF

kubectl apply -f pv-volume-pvc.yaml

kubectl get pvc

kubectl edit pvc pv-volume --record
```

```
[centos@kubeadm01 cka]$ kubectl apply -f pv-volume-pvc.yaml
persistentvolumeclaim/pv-volume created
pod/web-server created

[centos@kubeadm01 cka]$ kubectl get pvc
NAME        STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS      AGE
pv-volume   Pending                                      csi-hostpath-sc   29s

[centos@kubeadm01 cka]$ kubectl edit pvc pv-volume --record
...
resources:
    requests:
      storage: 70Mi
  storageClassName: csi-hostpath-sc
 ...
 :wq
```

### Monitor Pod

```
kubectl logs foobar | grep unable-to-access-website > /opt/KUTR00101/foobar
```

### SideCar Container

https://kubernetes.io/docs/concepts/cluster-administration/logging/#sidecar-container-with-logging-agent

https://raw.githubusercontent.com/kubernetes/website/main/content/en/examples/admin/logging/two-files-counter-pod-streaming-sidecar.yaml

```
# get the existing pod info
kubectl describe pod legacy-app
```

```yaml
  - name: busybox # update as busybox
    image: busybox
    args: [/bin/sh, -c, 'tail -n+1 -f /var/log/legacy-app.log'] # update as /var/log/legacy-app.log
    volumeMounts:
    - name: logs # update as logs
      mountPath: /var/log
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: legacy-app # update as legacy-app
spec:
  containers:
  - name: count # update as existing pod name
    image: busybox
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$i: $(date)" >> /var/log/legacy-app.log; # update as /var/log/legacy-app.log
        i=$((i+1));
        sleep 1;
      done      
    volumeMounts:
    - name: logs # update as logs
      mountPath: /var/log
  - name: busybox # update as busybox
    image: busybox
    args: [/bin/sh, -c, 'tail -n+1 -f /var/log/legacy-app.log'] # update as /var/log/legacy-app.log
    volumeMounts:
    - name: logs # update as logs
      mountPath: /var/log
  volumes:
  - name: logs # update as logs
    emptyDir: {}
```

```
kubectl apply -f sidecar-pod.yaml
```

### Pod CPU Usage

```
kubectl top pod -l name=cpu-user -A

echo 'cpu-user-1' >> /opt/KUTR00401/KUTR00401.txt
```

### Fix kubelet

```
ssh wk8s-node-0
sudo -i

systemctl status kubelet

systemctl enable kubelet
systemctl start kubelet
```
