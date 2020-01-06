[进阶提高篇]基于Kubernetes构建企业容器云
---

**参考资料:**

[基于Kubernetes构建企业容器云](http://www.devopsedu.com/front/couinfo/75)

### Kubernetes组件深入理解
---

组件架构图

![K8S Architecture](https://raw.githubusercontent.com/mcsrainbow/notes/master/k8s/images/k8s_architecture_01.jpg)

API Server

```
供Kubernetes API接口,主要处理REST操作以及更新Etcd中的对象.
所有资源增删改查的唯一入口.
```

Scheduler

```
绑定Pod到Node上,资源调度.
```

Controller Manager

```
所有其它集群级别的功能,目前由控制器Manager执行.资源对象的自动化控制中心.
```

Etcd

```
所有持久化的状态信息存储在Etcd中.
```

Client,UI,Scheduler,Controller Manager都是直接和API Server通信,API Server再跟Node上的Kubelet通信.

在Node层面的相关组件中:

Docker Engine

```
负责节点的容器的管理工作
```

Kubelet

```
管理Pods以及容器,镜像,Volume等,实现对集群对节点的管理
```

Kube-Proxy

```
提供网络代理以及负载均衡,实现与Service通讯
```

### 第一步:将应用封装到容器中
---

分层构建最佳实践

具体参考: [Docker实战与进阶](https://github.com/mcsrainbow/notes/blob/master/docker/docker_notes_advanced.md)

利用Harbor来管理镜像

### 第二步:将容器放置在Pod中
---

```yaml
apiVersion: v1                 # 版本号
kind: Pod                      # 资源类型 Pod
metadata:                      # 元数据
  name: nginx                  # metadata.name Pod的名称
  labels:                      # metadata.labels 自定义标签列表
    app: nginx
spec:                          # Pod中容器的详细定义
  containers:                  # spec.containers 容器列表
  - name: nginx                # spec.containers.name 容器名称
    image: nginx:1.13.12       # spec.containers.image 容器镜像名称
    ports:                     # 容器需要暴露的端口号列表
    - containerPort: 80        # 容器监听的端口号
```

Pod中可以运行多个紧耦合的容器,并且之间都可以用localhost相互访问

```
[root@linux-node1 ~]# cd /root/deploy/nginx/
[root@linux-node1 nginx]# vim nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.13.12
    ports:
    - containerPort: 80

[root@linux-node1 nginx]# kubectl create -f nginx-pod.yaml
pod "nginx-pod" created

[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
nginx-pod                           1/1       Running   0          4m

[root@linux-node1 nginx]# kubectl describe pod nginx-pod | tail -n 5
  Normal   Scheduled              2m                default-scheduler        Successfully assigned nginx-pod to 172.16.181.162
  Normal   SuccessfulMountVolume  2m                kubelet, 172.16.181.162  MountVolume.SetUp succeeded for volume "default-token-w5t7k"
  Normal   Pulled                 2m                kubelet, 172.16.181.162  Container image "nginx:1.13.12" already present on machine
  Normal   Created                2m                kubelet, 172.16.181.162  Created container
  Normal   Started                2m                kubelet, 172.16.181.162  Started container

[root@linux-node1 nginx]# kubectl get pod -o wide
NAME                                READY     STATUS    RESTARTS   AGE       IP           NODE
nginx-pod                           1/1       Running   0          7m        10.2.47.9    172.16.181.162

[root@linux-node1 nginx]# curl --head http://10.2.47.9
HTTP/1.1 200 OK
Server: nginx/1.13.12
Date: Fri, 03 Jan 2020 07:19:17 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 09 Apr 2018 16:01:09 GMT
Connection: keep-alive
ETag: "5acb8e45-264"
Accept-Ranges: bytes

[root@linux-node1 nginx]# kubectl logs pod/nginx-pod
10.2.85.0 - - [03/Jan/2020:07:19:17 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.29.0" "-"

[root@linux-node1 nginx]# kubectl delete -f nginx-pod.yaml
pod "nginx-pod" deleted

[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
```

配置Secret使用私有仓库

```
[root@linux-node1 nginx]# docker login -u mcsrainbow
Password:
Login Succeeded

root@linux-node1 nginx]# cat ~/.docker/config.json | base64
AAAJImF1dGhzIjogewoJCSJodHRwczovL2luZGV4LmRvY2tlci5pby92MS8iOiB7CgkJCSJhdAAA
BBBgImJXTnpjbUZwYm1KdmR6b3lOVEUxWkdRMFpRPT0iCgkJfQoJfSwKCSJIdHRwSGVhZGVycBBB
CCCKCQkiVXNlci1BZ2VudCI6ICJEb2NrZXItQ2xpZW50LzE5LjAzLjUgKGxpbnV4KSIKCX0KfCCC

[root@linux-node1 nginx]# mkdir /root/deploy/secret
[root@linux-node1 nginx]# cd /root/deploy/secret/
[root@linux-node1 secret]# vim hub-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: hub-secret
  namespace: default
data:
  .dockerconfigjson: AAAJImF1dGhzIjogewoJCSJodHRwczovL2luZGV4LmRvY2tlci5pby92MS8iOiB7CgkJCSJhdAAABBBgImJXTnpjbUZwYm1KdmR6b3lOVEUxWkdRMFpRPT0iCgkJfQoJfSwKCSJIdHRwSGVhZGVycBBBCCCKCQkiVXNlci1BZ2VudCI6ICJEb2NrZXItQ2xpZW50LzE5LjAzLjUgKGxpbnV4KSIKCX0KfCCC
type: kubernetes.io/dockerconfigjson

[root@linux-node1 secret]# kubectl create -f hub-secret.yaml
secret "hub-secret" created

[root@linux-node1 secret]# kubectl get secret
NAME                  TYPE                                  DATA      AGE
default-token-w5t7k   kubernetes.io/service-account-token   3         2d
hub-secret            kubernetes.io/dockerconfigjson        1         40s

[root@linux-node1 secret]# cd /root/deploy/nginx
[root@linux-node1 nginx]# vim nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: mcsrainbow/private:nginx_1.13.12
    ports:
    - containerPort: 80
  imagePullSecrets:
    - name: hub-secret
    
[root@linux-node1 nginx]# kubectl create -f nginx-pod.yaml
pod "nginx-pod" created

[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS              RESTARTS   AGE
nginx-pod                           0/1       ContainerCreating   0          18s

[root@linux-node1 nginx]# kubectl describe pod nginx-pod | tail -n 5
  Normal  SuccessfulMountVolume  55s   kubelet, 172.16.181.163  MountVolume.SetUp succeeded for volume "default-token-w5t7k"
  Normal  Pulling                53s   kubelet, 172.16.181.163  pulling image "mcsrainbow/private:nginx_1.13.12"
  Normal  Pulled                 35s   kubelet, 172.16.181.163  Successfully pulled image "mcsrainbow/private:nginx_1.13.12"
  Normal  Created                35s   kubelet, 172.16.181.163  Created container
  Normal  Started                35s   kubelet, 172.16.181.163  Started container

[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
nginx-pod                           1/1       Running   0          1m
```

名称为/pause的特殊容器

每个Pod都会有一个/pause容器,在Pod中启用PID命名空间,开启init进程

每个Pod都会分配一个IP地址和一个共享存储卷,Pod的IP地址是不固定的

```
[root@linux-node1 nginx]# docker ps
CONTAINER ID        IMAGE                                    COMMAND                  CREATED             STATUS              PORTS               NAMES
b1020fa84118        ae513a47849c                             "nginx -g 'daemon of…"   45 minutes ago      Up 45 minutes                           k8s_nginx_nginx-deployment-6c45fc49cb-sfdh9_default_b95fe08f-2df8-11ea-898d-000c298407a7_0
b80f00a81200        mirrorgooglecontainers/pause-amd64:3.0   "/pause"                 45 minutes ago      Up 45 minutes                           k8s_POD_nginx-deployment-6c45fc49cb-sfdh9_default_b95fe08f-2df8-11ea-898d-000c298407a7_0
66d517e18693        0c60bcf89900                             "/dashboard --insecu…"   46 minutes ago      Up 46 minutes                           k8s_kubernetes-dashboard_kubernetes-dashboard-66c9d98865-czvqw_kube-system_063572e6-2d3f-11ea-9dee-000c298407a7_2
97d4d81e74d3        d4b7466213fe                             "/coredns -conf /etc…"   46 minutes ago      Up 46 minutes                           k8s_coredns_coredns-77c989547b-gnxr8_kube-system_d7b2b40c-2d3b-11ea-9dee-000c298407a7_2
c130ade69609        mirrorgooglecontainers/pause-amd64:3.0   "/pause"                 46 minutes ago      Up 46 minutes                           k8s_POD_kubernetes-dashboard-66c9d98865-czvqw_kube-system_063572e6-2d3f-11ea-9dee-000c298407a7_2
f943ee4c8f54        mirrorgooglecontainers/pause-amd64:3.0   "/pause"                 46 minutes ago      Up 46 minutes                           k8s_POD_coredns-77c989547b-gnxr8_kube-system_d7b2b40c-2d3b-11ea-9dee-000c298407a7_2

[root@linux-node1 nginx]# grep google /usr/lib/systemd/system/kubelet.service
  --pod-infra-container-image=mirrorgooglecontainers/pause-amd64:3.0 \
```

### 第三步: 使用Controllers管理Pod
---

#### 使用Replication Controller管理Pod

RC是K8S集群中最早的保证Pod高可用的API对象.通过监控运行中的Pod来保证集群中运行指定数目的Pod副本.

指定的数目可以是多个也可以是一个,少于指定书目,RC就会启动运行新的副本,多于指定书目,RC就会杀死多余的副本.

即使在指定数目为1的情况下,通过RC运行Pod也比直接运行Pod更明智,因为RC也可以发挥它高可用的能力,保证永远有1个Pod在运行.

```
[root@linux-node1 nginx]# vim nginx-rc.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80

[root@linux-node1 nginx]# kubectl delete -f nginx-pod.yaml
pod "nginx-pod" deleted

[root@linux-node1 nginx]# kubectl create -f nginx-rc.yaml
replicationcontroller "nginx-rc" created

[root@linux-node1 nginx]# kubectl get pod -o wide
NAME             READY     STATUS    RESTARTS   AGE       IP            NODE
nginx-rc-dwt2x   1/1       Running   0          51s       10.2.102.10   172.16.181.163
nginx-rc-ltznk   1/1       Running   0          51s       10.2.85.12    172.16.181.161
nginx-rc-qnlvr   1/1       Running   0          51s       10.2.47.10    172.16.181.162

[root@linux-node1 nginx]# kubectl get rc -o wide
NAME       DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES          SELECTOR
nginx-rc   3         3         3         1m        nginx        nginx:1.13.12   app=nginx

[root@linux-node1 nginx]# kubectl describe rc nginx-rc
Name:         nginx-rc
Namespace:    default
Selector:     app=nginx
Labels:       app=nginx
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx:1.13.12
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                    Message
  ----    ------            ----  ----                    -------
  Normal  SuccessfulCreate  1m    replication-controller  Created pod: nginx-rc-dwt2x
  Normal  SuccessfulCreate  1m    replication-controller  Created pod: nginx-rc-qnlvr
  Normal  SuccessfulCreate  1m    replication-controller  Created pod: nginx-rc-ltznk

扩容  
[root@linux-node1 nginx]# kubectl scale rc nginx-rc --replicas=4
replicationcontroller "nginx-rc" scaled

[root@linux-node1 nginx]# kubectl get pods
NAME             READY     STATUS    RESTARTS   AGE
nginx-rc-88tfg   1/1       Running   0          7s
nginx-rc-dwt2x   1/1       Running   0          5m
nginx-rc-ltznk   1/1       Running   0          5m
nginx-rc-qnlvr   1/1       Running   0          5m

缩容
[root@linux-node1 nginx]# kubectl scale rc nginx-rc --replicas=2
replicationcontroller "nginx-rc" scaled

[root@linux-node1 nginx]# kubectl get pods
NAME             READY     STATUS    RESTARTS   AGE
nginx-rc-dwt2x   1/1       Running   0          6m
nginx-rc-qnlvr   1/1       Running   0          6m

滚动升级
[root@linux-node1 nginx]# kubectl rolling-update nginx-rc --image=nginx:1.14.0
Created nginx-rc-60495ed58ba7148ad3e0c99923e0981e
Scaling up nginx-rc-60495ed58ba7148ad3e0c99923e0981e from 0 to 2, scaling down nginx-rc from 2 to 0 (keep 2 pods available, don't exceed 3 pods)
Scaling nginx-rc-60495ed58ba7148ad3e0c99923e0981e up to 1
Scaling nginx-rc down to 1
Scaling nginx-rc-60495ed58ba7148ad3e0c99923e0981e up to 2
Scaling nginx-rc down to 0
Update succeeded. Deleting old controller: nginx-rc
Renaming nginx-rc-60495ed58ba7148ad3e0c99923e0981e to nginx-rc
replicationcontroller "nginx-rc" rolling updated

[root@linux-node1 nginx]# kubectl get rc -o wide
NAME       DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES         SELECTOR
nginx-rc   2         2         2         23s       nginx        nginx:1.14.0   app=nginx,deployment=60495ed58ba7148ad3e0c99923e0981e
```

#### 使用Replica Set副本集和Deployment管理Pod

RS是新一代RC,提供同样的高可用能力,区别主要在于RS后来居上,能支持更多的匹配模式.副本及对象一般不单独使用,而是作为部署的理想状态参数使用.

是K8S 1.2中出现的概念,是RC的升级.一般和Deployment共同使用.

```
[root@linux-node1 nginx]# kubectl delete -f nginx-rc.yaml
replicationcontroller "nginx-rc" deleted

[root@linux-node1 nginx]# vim nginx-rs.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
        
[root@linux-node1 nginx]# kubectl create -f nginx-rs.yaml
replicaset.apps "nginx-rs" created

[root@linux-node1 nginx]# kubectl get rs -o wide
NAME       DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES          SELECTOR
nginx-rs   3         3         3         52s       nginx        nginx:1.13.12   app=nginx

[root@linux-node1 nginx]# kubectl get pods
NAME             READY     STATUS    RESTARTS   AGE
nginx-rs-d4hjv   1/1       Running   0          57s
nginx-rs-q59p7   1/1       Running   0          57s
nginx-rs-z4t72   1/1       Running   0          57s
```

但在实际生产环境中,并不建议直接使用RC和RS来管理资源

K8S的一个重要贡献是改变了整个应用的发布模式

在生产环境中,由Deployment来管理整个应用的发布,RS的创建等

Deployment表示用户对K8S集群的一次更新操作.Deployment是一个比RS应用模式更广的API对象.

可以是创建一个新服务,更新一个新的服务,也可以是滚动升级一个服务.滚动升级一个服务,实际是创建一个新的RS,然后逐渐将新RS中副本数增加到理想状态,将旧RS中的副本数减小到0的复合操作.

这样一个复合操作用一个RS是不太好描述的,所以用一个更通用的Deployment来描述.

```
[root@linux-node1 nginx]# kubectl delete -f nginx-rs.yaml
replicaset.apps "nginx-rs" deleted

[root@linux-node1 nginx]# vim nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80

[root@linux-node1 nginx]# kubectl create -f nginx-deployment.yaml
deployment.apps "nginx-deployment" created

[root@linux-node1 nginx]# kubectl get deploy -o wide
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINERS   IMAGES          SELECTOR
nginx-deployment   3         3         3            3           18s       nginx        nginx:1.13.12   app=nginx

[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-6c45fc49cb-4j4bv   1/1       Running   0          1m
nginx-deployment-6c45fc49cb-6jmc9   1/1       Running   0          1m
nginx-deployment-6c45fc49cb-trxgp   1/1       Running   0          1m

[root@linux-node1 nginx]# kubectl delete -f nginx-deployment.yaml
deployment.apps "nginx-deployment" deleted

启用历史记录
[root@linux-node1 nginx]# kubectl create -f nginx-deployment.yaml --record
deployment.apps "nginx-deployment" created

[root@linux-node1 nginx]# kubectl get pods --show-labels
NAME                                READY     STATUS    RESTARTS   AGE       LABELS
nginx-deployment-6c45fc49cb-58vxp   1/1       Running   0          1m        app=nginx,pod-template-hash=2701970576
nginx-deployment-6c45fc49cb-6n2sp   1/1       Running   0          1m        app=nginx,pod-template-hash=2701970576
nginx-deployment-6c45fc49cb-hr96x   1/1       Running   0          1m        app=nginx,pod-template-hash=2701970576

更新
[root@linux-node1 nginx]# kubectl set image deployment/nginx-deployment nginx=nginx:1.14.0
deployment.apps "nginx-deployment" image updated

[root@linux-node1 nginx]# kubectl rollout status deployment/nginx-deployment
deployment "nginx-deployment" successfully rolled out

[root@linux-node1 nginx]# kubectl get deployment -o wide
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINERS   IMAGES         SELECTOR
nginx-deployment   3         3         3            3           3m        nginx        nginx:1.14.0   app=nginx

查看历史
[root@linux-node1 nginx]# kubectl rollout history deployment/nginx-deployment
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
1         kubectl create --filename=nginx-deployment.yaml --record=true
2         kubectl set image deployment/nginx-deployment nginx=nginx:1.14.0

[root@linux-node1 nginx]# kubectl rollout history deployment/nginx-deployment --revision=1
deployments "nginx-deployment" with revision #1
Pod Template:
  Labels:	app=nginx
	pod-template-hash=2701970576
  Annotations:	kubernetes.io/change-cause=kubectl create --filename=nginx-deployment.yaml --record=true
  Containers:
   nginx:
    Image:	nginx:1.13.12
    Port:	80/TCP
    Host Port:	0/TCP
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>

回滚
[root@linux-node1 nginx]# kubectl rollout undo deployment/nginx-deployment
deployment.apps "nginx-deployment"

[root@linux-node1 nginx]# kubectl get deploy -o wide
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINERS   IMAGES          SELECTOR
nginx-deployment   3         4         3            3           5m        nginx        nginx:1.13.12   app=nginx

[root@linux-node1 nginx]# kubectl rollout history deployment/nginx-deployment
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
2         kubectl set image deployment/nginx-deployment nginx=nginx:1.14.0
3         kubectl create --filename=nginx-deployment.yaml --record=true

[root@linux-node1 nginx]# kubectl rollout undo deployment/nginx-deployment --to-revision=2
deployment.apps "nginx-deployment"

[root@linux-node1 nginx]# kubectl get deploy -o wide
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINERS   IMAGES         SELECTOR
nginx-deployment   3         3         3            3           7m        nginx        nginx:1.14.0   app=nginx

[root@linux-node1 nginx]# kubectl rollout history deployment/nginx-deployment
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
3         kubectl create --filename=nginx-deployment.yaml --record=true
4         kubectl set image deployment/nginx-deployment nginx=nginx:1.14.0

扩容
[root@linux-node1 nginx]# kubectl scale deployment nginx-deployment --replicas 4
deployment.extensions "nginx-deployment" scaled

[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-78995fd4d6-hmkf5   1/1       Running   0          1m
nginx-deployment-78995fd4d6-k78t7   1/1       Running   0          11s
nginx-deployment-78995fd4d6-lw8wp   1/1       Running   0          1m
nginx-deployment-78995fd4d6-v2jxb   1/1       Running   0          1m

缩容
[root@linux-node1 nginx]# kubectl scale deployment nginx-deployment --replicas 2
deployment.extensions "nginx-deployment" scaled
[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS        RESTARTS   AGE
nginx-deployment-78995fd4d6-hmkf5   0/1       Terminating   0          1m
nginx-deployment-78995fd4d6-k78t7   0/1       Terminating   0          32s
nginx-deployment-78995fd4d6-lw8wp   1/1       Running       0          2m
nginx-deployment-78995fd4d6-v2jxb   1/1       Running       0          2m

[root@linux-node1 nginx]# kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-78995fd4d6-lw8wp   1/1       Running   0          2m
nginx-deployment-78995fd4d6-v2jxb   1/1       Running   0          2m
```

#### 使用DamonSet管理Pod和Node Labels

什么是DaemonSet?

- DaemonSet确保全部(或者部分)Node上运行一个Pod的副本.当有Node加入集群时,也会为他们新增一个Pod,且仅有一个.

- 当有Node从集群移除时,这些Pod也会被回收.

- 删除DaemonSet将会删除它创建的所有Pod.

DaemonSet应用场景

- 运行集群存储daemon,例如在每个Node上运行glusterd,ceph.

- 在每个Node上运行日志手机daemon,例如filebeat,logstash

- 在每个Node上运行监控daemon,例如Prometheus Node Exporter,Datadog Agent, Zabbix Agent

```
[root@linux-node1 nginx]# kubectl delete -f nginx-deployment.yaml
deployment.apps "nginx-deployment" deleted

[root@linux-node1 nginx]# vim nginx-daemonset.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80

[root@linux-node1 nginx]# diff nginx-daemonset.yaml nginx-deployment.yaml
2c2
< kind: DaemonSet
---
> kind: Deployment
4c4
<   name: nginx-daemonset
---
>   name: nginx-deployment
7a8
>   replicas: 3

[root@linux-node1 nginx]# kubectl create -f nginx-daemonset.yaml
daemonset.apps "nginx-daemonset" created

[root@linux-node1 nginx]# kubectl get pod -o wide
NAME                    READY     STATUS    RESTARTS   AGE       IP            NODE
nginx-daemonset-hh2sg   1/1       Running   0          16s       10.2.47.18    172.16.181.162
nginx-daemonset-jttd7   1/1       Running   0          16s       10.2.85.20    172.16.181.161
nginx-daemonset-llgjj   1/1       Running   0          16s       10.2.102.19   172.16.181.163

[root@linux-node1 nginx]# kubectl get daemonset
NAME              DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
nginx-daemonset   3         3         3         3            3           <none>          48s
```

什么是Node Selector?

将Pod运行在指定的Node上.例如将某类应用运行在SSD硬盘的服务器上.

Node Selector操作,打标签,选择标签

```
[root@linux-node1 nginx]# kubectl label nodes 172.16.181.163 disktype=ssd
node "172.16.181.163" labeled

[root@linux-node1 nginx]# kubectl get nodes --show-labels
NAME             STATUS    ROLES     AGE       VERSION   LABELS
172.16.181.161   Ready     <none>    1d        v1.10.1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=172.16.181.161
172.16.181.162   Ready     <none>    1d        v1.10.1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=172.16.181.162
172.16.181.163   Ready     <none>    1d        v1.10.1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,disktype=ssd,kubernetes.io/hostname=172.16.181.163

[root@linux-node1 nginx]# kubectl delete -f nginx-daemonset.yaml
daemonset.apps "nginx-daemonset" deleted

[root@linux-node1 nginx]# vim nginx-daemonset.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
      nodeSelector:
        disktype: ssd

[root@linux-node1 nginx]# kubectl create -f nginx-daemonset.yaml
daemonset.apps "nginx-daemonset" created

[root@linux-node1 nginx]# kubectl get daemonset -o wide
NAME              DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE       CONTAINERS   IMAGES          SELECTOR
nginx-daemonset   1         1         1         1            1           disktype=ssd    15s       nginx        nginx:1.13.12   app=nginx

[root@linux-node1 nginx]# kubectl get pods -o wide
NAME                    READY     STATUS    RESTARTS   AGE       IP            NODE
nginx-daemonset-gxsbm   1/1       Running   0          31s       10.2.102.20   172.16.181.163
```


### 第四步:使用Service管理Pod访问
---

RC,RS和Deployment只是保证了支撑服务的Pod的数量,但是没有解决如何访问这些服务的问题.一个Pod只是一个运行服务的实例,随时可能在一个节点上停止,在另一个节点以一个新的IP启动一个新的Pod,因此不能以确定的IP和端口好提供服务.

要稳定地提供服务需要服务发现和负载均衡能力.服务发现完成的工作,是针对客户端访问的服务,找到对应的后端服务实例.

在K8S集群中,客户端需要访问的服务就是Service对象.每个Service会对应一个集群内部有效的虚拟IP,集群内部通过虚拟IP访问一个服务.

```
[root@linux-node1 nginx]# kubectl create -f nginx-deployment.yaml --record
deployment.apps "nginx-deployment" created

[root@linux-node1 nginx]# vim nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

[root@linux-node1 nginx]# kubectl create -f nginx-service.yaml
service "nginx-service" created

[root@linux-node1 nginx]# kubectl get svc -o wide
NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE       SELECTOR
kubernetes      ClusterIP   10.1.0.1      <none>        443/TCP   2d        <none>
nginx-service   ClusterIP   10.1.141.44   <none>        80/TCP    36s       app=nginx

[root@linux-node1 nginx]# curl --head http://10.1.141.44
HTTP/1.1 200 OK
Server: nginx/1.13.12
Date: Fri, 03 Jan 2020 09:56:14 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 09 Apr 2018 16:01:09 GMT
Connection: keep-alive
ETag: "5acb8e45-264"
Accept-Ranges: bytes

[root@linux-node1 nginx]# vim nginx-service.yaml
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 80
  - name: https
    protocol: TCP
    port: 443
    targetPort: 443

[root@linux-node1 nginx]#  kubectl apply -f nginx-service.yaml
service "nginx-service" configured

[root@linux-node1 nginx]# kubectl get svc -o wide
NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE       SELECTOR
kubernetes      ClusterIP   10.1.0.1      <none>        443/TCP          2d        <none>
nginx-service   ClusterIP   10.1.141.44   <none>        80/TCP,443/TCP   30m       app=nginx
```

### 第五步:使用Ingress提供外部访问
---

Node IP:节点设备的IP,如物理机,虚拟机等容器宿主的实际IP

Pod IP: Pod的IP地址,是根据docker0网格IP段进行分配的

Cluster IP: Service的IP,是一个虚拟IP,仅作用域service对象,由K8S管理和分配,需要结合service port才能使用,单独的IP没有通信功能,集群外访问需要一些修改

在K8S集群内部,Node IP,Pod IP,Cluster IP的通信机制是由K8S制定的路由规则,不是IP路由.

```
[root@linux-node1 nginx]# vim nginx-service.yaml
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 80
  - name: https
    protocol: TCP
    port: 443
    targetPort: 443
  type: NodePort

[root@linux-node1 nginx]# kubectl apply -f nginx-service.yaml
service "nginx-service" configured

随机端口范围由NODE_PORT_RANGE设置
[root@linux-node1 nginx]# kubectl get svc -o wide
NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE       SELECTOR
kubernetes      ClusterIP   10.1.0.1      <none>        443/TCP                      2d        <none>
nginx-service   NodePort    10.1.141.44   <none>        80:24938/TCP,443:37101/TCP   44m       app=nginx

[root@linux-node1 nginx]# kubectl get pod -o wide
NAME                                READY     STATUS    RESTARTS   AGE       IP            NODE
nginx-daemonset-gxsbm               1/1       Running   0          55m       10.2.102.20   172.16.181.163
nginx-deployment-6c45fc49cb-gzmcp   1/1       Running   0          52m       10.2.85.21    172.16.181.161
nginx-deployment-6c45fc49cb-kq6mz   1/1       Running   0          52m       10.2.102.21   172.16.181.163
nginx-deployment-6c45fc49cb-pj77d   1/1       Running   0          52m       10.2.47.19    172.16.181.162

[root@linux-node1 nginx]# curl --head http://linux-node1:24938
HTTP/1.1 200 OK
Server: nginx/1.13.12
Date: Fri, 03 Jan 2020 10:40:25 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 09 Apr 2018 16:01:09 GMT
Connection: keep-alive
ETag: "5acb8e45-264"
Accept-Ranges: bytes

[root@linux-node1 nginx]# ipvsadm -Ln | grep 24938 -A4
TCP  172.16.181.161:24938 rr
  -> 10.2.47.19:80                Masq    1      0          0
  -> 10.2.85.21:80                Masq    1      0          0
  -> 10.2.102.20:80               Masq    1      0          0
  -> 10.2.102.21:80               Masq    1      0          0
```

Internet - Ingress(IngressController) - Services(Kube-Proxy/LVS)

Ingress通过API监听后端Pod的状态,动态的更新配置,达到服务发现的功能,实现七层代理

Traefik 云原生边界路由: https://traefik.io

拥有自己的Web界面,并且能和K8S,Mesos等编排工具集成

通过打标签,将Traefik部署到特定的边缘节点上,使用hostPort直接指定Node上的特定nodePort

```
[root@linux-node1 nginx]# mkdir /root/deploy/ingress
[root@linux-node1 nginx]# cd /root/deploy/ingress/

[root@linux-node1 ingress]# kubectl label nodes 172.16.181.161 edgenode=true
node "172.16.181.161" labeled

[root@linux-node1 ingress]# kubectl get nodes --show-labels
NAME             STATUS    ROLES     AGE       VERSION   LABELS
172.16.181.161   Ready     <none>    1d        v1.10.1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,edgenode=true,kubernetes.io/hostname=172.16.181.161
172.16.181.162   Ready     <none>    1d        v1.10.1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=172.16.181.162
172.16.181.163   Ready     <none>    1d        v1.10.1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,disktype=ssd,kubernetes.io/hostname=172.16.181.163

[root@linux-node1 ingress]# vim daemonset.yml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: traefik-ingress-lb
  namespace: kube-system
  labels:
    k8s-app: traefik-ingress-lb
spec:
  template:
    metadata:
      labels:
        k8s-app: traefik-ingress-lb
        name: traefik-ingress-lb
    spec:
      terminationGracePeriodSeconds: 60
      hostNetwork: true
      restartPolicy: Always
      serviceAccountName: ingress
      containers:
      - image: traefik:v1.6
        name: traefik-ingress-lb
        resources:
          limits:
            cpu: 200m
            memory: 80Mi
          requests:
            cpu: 100m
            memory: 50Mi
        ports:
        - name: http
          containerPort: 80
          hostPort: 80
        - name: admin
          containerPort: 8580
          hostPort: 8580
        args:
        - --web
        - --web.address=:8580
        - --kubernetes
      nodeSelector:
        edgenode: "true"

[root@linux-node1 ingress]# vim ingress-rbac.yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ingress
  namespace: kube-system

---

kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: ingress
subjects:
  - kind: ServiceAccount
    name: ingress
    namespace: kube-system
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io

[root@linux-node1 ingress]# vim traefik-ui.yml
apiVersion: v1
kind: Service
metadata:
  name: traefik-web-ui
  namespace: kube-system
spec:
  selector:
    k8s-app: traefik-ingress-lb
  ports:
  - name: web
    port: 80
    targetPort: 8580
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: traefik-web-ui
  namespace: kube-system
spec:
  rules:
  - host: traefik-ui.local
    http:
      paths:
      - path: /
        backend:
          serviceName: traefik-web-ui
          servicePort: web

[root@linux-node1 ingress]# kubectl get pods -n kube-system
NAME                                    READY     STATUS    RESTARTS   AGE
coredns-77c989547b-gnxr8                1/1       Running   2          1d
coredns-77c989547b-tr784                1/1       Running   0          3h
kubernetes-dashboard-66c9d98865-czvqw   1/1       Running   2          1d

[root@linux-node1 ingress]# kubectl create -f .
daemonset.extensions "traefik-ingress-lb" created
serviceaccount "ingress" created
clusterrolebinding.rbac.authorization.k8s.io "ingress" created
service "traefik-web-ui" created
ingress.extensions "traefik-web-ui" created

[root@linux-node1 ingress]# kubectl get pods -n kube-system
NAME                                    READY     STATUS    RESTARTS   AGE
coredns-77c989547b-gnxr8                1/1       Running   2          1d
coredns-77c989547b-tr784                1/1       Running   0          3h
kubernetes-dashboard-66c9d98865-czvqw   1/1       Running   2          1d
traefik-ingress-lb-vhmmk                1/1       Running   0          32s

[root@linux-node1 ingress]# netstat -lntp | grep -w 8580
tcp6       0      0 :::8580                   :::*                    LISTEN      70419/traefik
```

直接访问http://linux-node1.example.com:8580

![Traefik](https://raw.githubusercontent.com/mcsrainbow/notes/master/k8s/images/k8s_traefik_01.jpg)

然后创建一个Domain

```
[root@linux-node1 ingress]# kubectl get svc
NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
kubernetes      ClusterIP   10.1.0.1      <none>        443/TCP                      2d
nginx-service   NodePort    10.1.141.44   <none>        80:24938/TCP,443:37101/TCP   1h

[root@linux-node1 ingress]# cd /root/deploy/nginx/
[root@linux-node1 nginx]# vim nginx-ingress.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: nginx-ingress
spec:
  rules:
  - host: nginx.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: nginx-service
          servicePort: 80

[root@linux-node1 nginx]# kubectl create -f nginx-ingress.yaml
ingress.extensions "nginx-ingress" created

[root@linux-node1 nginx]# kubectl get ingress
NAME            HOSTS               ADDRESS   PORTS     AGE
nginx-ingress   nginx.example.com             80        26s
```

在Traefik中可以看到刚刚添加的Ingress Domain

![Traefik](https://raw.githubusercontent.com/mcsrainbow/notes/master/k8s/images/k8s_traefik_02.jpg)

在本地绑定nginx.example.com到172.16.181.161,并通过浏览器直接访问

![Traefik](https://raw.githubusercontent.com/mcsrainbow/notes/master/k8s/images/k8s_traefik_03.jpg)

### 第六步:使用PV和PVC管理数据存储
---

在所有节点上安装NFS软件包

```
[root@linux-node1 ~]# yum install -y nfs-utils rpcbind
```

在linux-node1上创建一个数据目录并启用NFS服务

```
[root@linux-node1 ~]# mkdir -p /data/k8s-nfs
[root@linux-node1 ~]# vim /etc/exports
/mn *(rw,sync,no_root_squash)

[root@linux-node1 ~]# systemctl enable rpcbind nfs
[root@linux-node1 ~]# systemctl start rpcbind nfs
[root@linux-node1 ~]# systemctl status rpcbind nfs
```

创建一个基于NFS的持久卷

```
[root@linux-node1 ~]# mkdir /root/deploy/volume
[root@linux-node1 ~]# cd /root/deploy/volume/
[root@linux-node1 volume]# vim nfs-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-demo
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: nfs
  nfs:
    path: /data/k8s-nfs/pv-demo
    server: 172.16.181.161

[root@linux-node1 volume]# kubectl create -f nfs-pv.yaml
persistentvolume "pv-demo" created

[root@linux-node1 volume]# kubectl get pv
NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM     STORAGECLASS   REASON    AGE
pv-demo   1Gi        RWO            Recycle          Available             nfs                      1m
```

声明一个用户存储的请求,基于刚刚创建的持久卷

```
[root@linux-node1 volume]# vim nfs-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-demo
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: nfs

[root@linux-node1 volume]# kubectl create -f nfs-pvc.yaml
persistentvolumeclaim "pvc-demo" created

[root@linux-node1 volume]# kubectl get pvc
NAME       STATUS    VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-demo   Bound     pv-demo   1Gi        RWO            nfs            13s
```

使用PVC,并将其mount到Nginx容器

```
[root@linux-node1 volume]# mkdir /data/k8s-nfs/pv-demo

[root@linux-node1 pvc-demo]# cd /root/deploy/nginx/
[root@linux-node1 nginx]# vim nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: pvc-demo
      volumes:
      - name: pvc-demo
        persistentVolumeClaim:
          claimName: pvc-demo

[root@linux-node1 nginx]# kubectl apply -f nginx-deployment.yaml
deployment.apps "nginx-deployment" configured

[root@linux-node1 volume]# cd /data/k8s-nfs/pv-demo
[root@linux-node1 pvc-demo]# vim 50x.html
<!DOCTYPE html>
<html>
<head>
<title>Error</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>An error occurred.</h1>
<p>Sorry, the page you are looking for is currently unavailable.<br/>
Please try again later.</p>
<p>If you are the system administrator of this resource then you should check
the <a href="http://nginx.org/r/error_log">error log</a> for details.</p>
<p><em>Faithfully yours, nginx.</em></p>
</body>
</html>

[root@linux-node1 pvc-demo]# vim index.html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
<p>linux-node1.example.com:/data/k8s-nfs/pvc-demo/index.html</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

重新访问http://nginx.example.com即可看到变更后的新index.html页面