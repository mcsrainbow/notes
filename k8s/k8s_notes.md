基于Kubernetes构建企业容器云
---

### Kubernetes 架构介绍和集群规划
----

Kubernetes不仅仅只支持Docker容器
 
3台虚拟机1台控制节点Master，2台计算节点Node
 
控制节点Master包含API Server, Scheduler, Controller Manager，Etcd配置管理工具
 
计算节点Node包含Kubelet, Kube-Proxy
 
底层采用Docker容器，Flannel网络组建
 
#### API Server
```
提供供Kubernetes API接口，主要处理REST操作以及更新ETCD中的对象
 
所有资源增删改查的唯一入口
```
 
#### Scheduler
```
资源调度，负责Pod到Node的调度
```
 
#### Controller Manager
```
所有其它集群级别的功能，目前由控制器Manager执行。资源对象的自动化控制中心
```
 
#### ETCD
```
所有持久化的状态信息存储在ETCD中
```

#### Kubelet
```
管理Pods以及容器，镜像，Volume等，实现对集群对节点的管理
 
部署在每个Node上的Agent，负责与API Server之间的通信，执行API Server的指令
```
 
#### Kube-Proxy
```
提供网络代理以及负载均衡，实现与Service通信
 
底层可调用LVS或iptables
```
 
 
#### Docker Engine
```
负责节点的容器的管理工作
```


#### 环境准备
```
软件包准备/opt/kubernetes目录下
 
使用二进制方式进行部署
```

### CA证书创建和分支
----

#### 系统初始化

在所有节点上安装Docker，并创建Kubernetes应用目录

```
[root@linux-node1 ~]# yum install -y yum-utils device-mapper-persistent-data lvm2

[root@linux-node1 ~]# yum -y install docker-ce

[root@linux-node1 ~]# systemctl start docker

[root@linux-node1 ~]# mkdir -p /opt/kubernetes/{cfg,bin,ssl,log}
```

在linux-node1上下载并解压缩离线二进制软件包

```
[root@linux-node1 ~]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1

[root@linux-node1 k8s-v1.10.1]# tar zxf kubernetes.tar.gz
[root@linux-node1 k8s-v1.10.1]# tar zxf kubernetes-server-linux-amd64.tar.gz
[root@linux-node1 k8s-v1.10.1]# tar zxf kubernetes-client-linux-amd64.tar.gz
[root@linux-node1 k8s-v1.10.1]# tar zxf kubernetes-node-linux-amd64.tar.gz

[root@linux-node1 k8s-v1.10.1]# vim ~/.bash_profile
export PATH=$PATH:$HOME/bin:/opt/kubernetes/bin
```

使用cfssl来配置生成证书，更简单

官方下载地址: https://pkg.cfssl.org

```
[root@linux-node1 ~]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1
[root@linux-node1 ~]# chmod +x cfssl*
[root@linux-node1 ~]# mv cfssl-certinfo_linux-amd64 /opt/kubernetes/bin/cfssl-certinfo
[root@linux-node1 ~]# mv cfssljson_linux-amd64 /opt/kubernetes/bin/cfssljson
[root@linux-node1 ~]# mv cfssl_linux-amd64 /opt/kubernetes/bin/cfssl

[root@linux-node1 ~]# mkdir ssl
[root@linux-node1 ~]# cd ssl
[root@linux-node1 ssl]# cfssl print-defaults config > config.json
[root@linux-node1 ssl]# cfssl print-defaults csr > csr.json

[root@linux-node1 ssl]# vim ca-config.json
{
  "signing": {
    "default": {
      "expiry": "8760h"
    },
    "profiles": {
      "kubernetes": {
        "usages": [
            "signing",
            "key encipherment",
            "server auth",
            "client auth"
        ],
        "expiry": "8760h"
      }
    }
  }
}

[root@linux-node1 ssl]# vim ca-csr.json
{
  "CN": "kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "GuangZhou",
      "L": "ShenZhen",
      "O": "k8s",
      "OU": "System"
    }
  ]
}

[root@linux-node1 ssl]# cfssl gencert -initca ca-csr.json | cfssljson -bare ca
2019/12/31 17:22:35 [INFO] generating a new CA key and certificate from CSR
2019/12/31 17:22:35 [INFO] generate received request
2019/12/31 17:22:35 [INFO] received CSR
2019/12/31 17:22:35 [INFO] generating key: rsa-2048
2019/12/31 17:22:35 [INFO] encoded CSR
2019/12/31 17:22:35 [INFO] signed certificate with serial number 682653602994288342726249941891732910439527798962

[root@linux-node1 ssl]# ls -l
-rw-r--r-- 1 root root  290 Dec 31 17:19 ca-config.json
-rw-r--r-- 1 root root 1001 Dec 31 17:22 ca.csr
-rw-r--r-- 1 root root  209 Dec 31 17:21 ca-csr.json
-rw------- 1 root root 1675 Dec 31 17:22 ca-key.pem
-rw-r--r-- 1 root root 1363 Dec 31 17:22 ca.pem
-rw-r--r-- 1 root root  567 Dec 31 17:19 config.json
-rw-r--r-- 1 root root  287 Dec 31 17:19 csr.json

[root@linux-node1 ssl]# cd
[root@linux-node1 ~]# ssh-keygen
[root@linux-node1 ~]# ssh-copy-id linux-node2
[root@linux-node1 ~]# ssh-copy-id linux-node3

[root@linux-node1 ~]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/ssl/ 
[root@linux-node1 ssl]# cp ca.csr ca.pem ca-key.pem ca-config.json /opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp ca.csr ca.pem ca-key.pem ca-config.json linux-node2:/opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp ca.csr ca.pem ca-key.pem ca-config.json linux-node3:/opt/kubernetes/ssl/
```

### ETCD集群部署
---

Kubernetes各个服务都是无状态的 https://github.com/coreos/etcd

```
[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/
[root@linux-node1 k8s-v1.10.1]# tar zxf etcd-v3.2.18-linux-amd64.tar.gz
[root@linux-node1 k8s-v1.10.1]# cd etcd-v3.2.18-linux-amd64
[root@linux-node1 etcd-v3.2.18-linux-amd64]# cp etcd etcdctl /opt/kubernetes/bin/
[root@linux-node1 etcd-v3.2.18-linux-amd64]# scp etcd etcdctl linux-node2:/opt/kubernetes/bin/
[root@linux-node1 etcd-v3.2.18-linux-amd64]# scp etcd etcdctl linux-node3:/opt/kubernetes/bin/

[root@linux-node1 etcd-v3.2.18-linux-amd64]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/ssl

[root@linux-node1 ssl]# vim etcd-csr.json
{
  "CN": "etcd",
  "hosts": [
    "127.0.0.1",
"172.16.181.161",
"172.16.181.162",
"172.16.181.163"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "GuangZhou",
      "L": "ShenZhen",
      "O": "k8s",
      "OU": "System"
    }
  ]
}

[root@linux-node1 ssl]# cfssl gencert -ca=/opt/kubernetes/ssl/ca.pem \
  -ca-key=/opt/kubernetes/ssl/ca-key.pem \
  -config=/opt/kubernetes/ssl/ca-config.json \
  -profile=kubernetes etcd-csr.json | cfssljson -bare etcd

[root@linux-node1 ssl]# cp etcd*.pem /opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp etcd*.pem linux-node2:/opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp etcd*.pem linux-node3:/opt/kubernetes/ssl/

[root@linux-node1 ssl]# vim /opt/kubernetes/cfg/etcd.conf
#[member]
ETCD_NAME="etcd-node1"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
#ETCD_SNAPSHOT_COUNTER="10000"
#ETCD_HEARTBEAT_INTERVAL="100"
#ETCD_ELECTION_TIMEOUT="1000"
ETCD_LISTEN_PEER_URLS="https://172.16.181.161:2380"
ETCD_LISTEN_CLIENT_URLS="https://172.16.181.161:2379,https://127.0.0.1:2379"
#ETCD_MAX_SNAPSHOTS="5"
#ETCD_MAX_WALS="5"
#ETCD_CORS=""
#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://172.16.181.161:2380"
# if you use different ETCD_NAME (e.g. test),
# set ETCD_INITIAL_CLUSTER value for this name, i.e. "test=http://..."
ETCD_INITIAL_CLUSTER="etcd-node1=https://172.16.181.161:2380,etcd-node2=https://172.16.181.162:2380,etcd-node3=https://172.16.181.163:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="k8s-etcd-cluster"
ETCD_ADVERTISE_CLIENT_URLS="https://172.16.181.161:2379"
#[security]
CLIENT_CERT_AUTH="true"
ETCD_CA_FILE="/opt/kubernetes/ssl/ca.pem"
ETCD_CERT_FILE="/opt/kubernetes/ssl/etcd.pem"
ETCD_KEY_FILE="/opt/kubernetes/ssl/etcd-key.pem"
PEER_CLIENT_CERT_AUTH="true"
ETCD_PEER_CA_FILE="/opt/kubernetes/ssl/ca.pem"
ETCD_PEER_CERT_FILE="/opt/kubernetes/ssl/etcd.pem"
ETCD_PEER_KEY_FILE="/opt/kubernetes/ssl/etcd-key.pem"

[root@linux-node1 ssl]# vim /etc/systemd/system/etcd.service
[Unit]
Description=Etcd Server
After=network.target

[Service]
Type=simple
WorkingDirectory=/var/lib/etcd
EnvironmentFile=-/opt/kubernetes/cfg/etcd.conf
# set GOMAXPROCS to number of processors
ExecStart=/bin/bash -c "GOMAXPROCS=$(nproc) /opt/kubernetes/bin/etcd"
Type=notify

[Install]
WantedBy=multi-user.target

[root@linux-node1 ssl]# scp /opt/kubernetes/cfg/etcd.conf linux-node2:/opt/kubernetes/cfg/
[root@linux-node1 ssl]# scp /etc/systemd/system/etcd.service linux-node2:/etc/systemd/system/
[root@linux-node1 ssl]# scp /opt/kubernetes/cfg/etcd.conf linux-node3:/opt/kubernetes/cfg/
[root@linux-node1 ssl]# scp /etc/systemd/system/etcd.service linux-node3:/etc/systemd/system/
```

在所有节点上启用并启动服务

```
[root@linux-node1 ssl]# vim /opt/kubernetes/cfg/etcd.conf
ETCD_NAME="etcd-node1"
...
ETCD_LISTEN_PEER_URLS="https://172.16.181.161:2380"
ETCD_LISTEN_CLIENT_URLS="https://172.16.181.161:2379,https://127.0.0.1:2379"
...
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://172.16.181.161:2380"
...
ETCD_ADVERTISE_CLIENT_URLS="https://172.16.181.161:2379"

[root@linux-node1 ssl]# systemctl daemon-reload
[root@linux-node1 ssl]# systemctl enable etcd
[root@linux-node1 ssl]# mkdir /var/lib/etcd
[root@linux-node1 ssl]# systemctl start etcd
[root@linux-node1 ssl]# systemctl status etcd

[root@linux-node1 ssl]# etcdctl --endpoints=https://172.16.181.161:2379 \
  --ca-file=/opt/kubernetes/ssl/ca.pem \
  --cert-file=/opt/kubernetes/ssl/etcd.pem \
  --key-file=/opt/kubernetes/ssl/etcd-key.pem cluster-health
member 80caba97aa2848a is healthy: got healthy result from https://172.16.181.162:2379
member 115056bae4e986b0 is healthy: got healthy result from https://172.16.181.161:2379
member 6bcf1c679e7e3933 is healthy: got healthy result from https://172.16.181.163:2379
cluster is healthy
```

### K8S Master节点部署
---

Pod是一个逻辑概念，里面包含了容器
controller-manager会确保集群中相关资源的数量和状态处于预期的工作状态

```
[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/kubernetes
[root@linux-node1 kubernetes]# cp server/bin/kube-apiserver /opt/kubernetes/bin/
[root@linux-node1 kubernetes]# cp server/bin/kube-controller-manager /opt/kubernetes/bin/
[root@linux-node1 kubernetes]# cp server/bin/kube-scheduler /opt/kubernetes/bin/

[root@linux-node1 kubernetes]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/ssl
[root@linux-node1 ssl]# vim kubernetes-csr.json
{
  "CN": "kubernetes",
  "hosts": [
    "127.0.0.1",
    "172.16.181.161",
    "10.1.0.1",
    "kubernetes",
    "kubernetes.default",
    "kubernetes.default.svc",
    "kubernetes.default.svc.cluster",
    "kubernetes.default.svc.cluster.local"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "GuangZhou",
      "L": "ShenZhen",
      "O": "k8s",
      "OU": "System"
    }
  ]
}

[root@linux-node1 ssl]# cfssl gencert -ca=/opt/kubernetes/ssl/ca.pem \
   -ca-key=/opt/kubernetes/ssl/ca-key.pem \
   -config=/opt/kubernetes/ssl/ca-config.json \
   -profile=kubernetes kubernetes-csr.json | cfssljson -bare kubernetes

[root@linux-node1 ssl]# cp kubernetes*.pem /opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp kubernetes*.pem linux-node2:/opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp kubernetes*.pem linux-node3:/opt/kubernetes/ssl/
```

部署API Server 服务

```
[root@linux-node1 ssl]# head -c 16 /dev/urandom | od -An -t x | tr -d ' '
2cc9c96b39ff8cc4c96c1311475912c9

[root@linux-node1 ssl]# vim /opt/kubernetes/ssl/bootstrap-token.csv
2cc9c96b39ff8cc4c96c1311475912c9,kubelet-bootstrap,10001,"system:kubelet-bootstrap"

[root@linux-node1 ssl]# vim /opt/kubernetes/ssl/basic-auth.csv
admin,admin,1
readonly,readonly,2

[root@linux-node1 ssl]# vim /usr/lib/systemd/system/kube-apiserver.service
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/GoogleCloudPlatform/kubernetes
After=network.target

[Service]
ExecStart=/opt/kubernetes/bin/kube-apiserver \
  --admission-control=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota,NodeRestriction \
  --bind-address=172.16.181.161 \
  --insecure-bind-address=127.0.0.1 \
  --authorization-mode=Node,RBAC \
  --runtime-config=rbac.authorization.k8s.io/v1 \
  --kubelet-https=true \
  --anonymous-auth=false \
  --basic-auth-file=/opt/kubernetes/ssl/basic-auth.csv \
  --enable-bootstrap-token-auth \
  --token-auth-file=/opt/kubernetes/ssl/bootstrap-token.csv \
  --service-cluster-ip-range=10.1.0.0/16 \
  --service-node-port-range=20000-40000 \
  --tls-cert-file=/opt/kubernetes/ssl/kubernetes.pem \
  --tls-private-key-file=/opt/kubernetes/ssl/kubernetes-key.pem \
  --client-ca-file=/opt/kubernetes/ssl/ca.pem \
  --service-account-key-file=/opt/kubernetes/ssl/ca-key.pem \
  --etcd-cafile=/opt/kubernetes/ssl/ca.pem \
  --etcd-certfile=/opt/kubernetes/ssl/kubernetes.pem \
  --etcd-keyfile=/opt/kubernetes/ssl/kubernetes-key.pem \
  --etcd-servers=https://172.16.181.161:2379,https://172.16.181.162:2379,https://172.16.181.163:2379 \
  --enable-swagger-ui=true \
  --allow-privileged=true \
  --audit-log-maxage=30 \
  --audit-log-maxbackup=3 \
  --audit-log-maxsize=100 \
  --audit-log-path=/opt/kubernetes/log/api-audit.log \
  --event-ttl=1h \
  --v=2 \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/log
Restart=on-failure
RestartSec=5
Type=notify
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target

[root@linux-node1 ssl]# systemctl daemon-reload
[root@linux-node1 ssl]# systemctl enable kube-apiserver
[root@linux-node1 ssl]# systemctl start kube-apiserver

[root@linux-node1 ssl]# systemctl status kube-apiserver

[root@linux-node1 ssl]# netstat -lntp
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 172.16.181.161:6443     0.0.0.0:*               LISTEN      13948/kube-apiserver
tcp        0      0 172.16.181.161:2379     0.0.0.0:*               LISTEN      13121/etcd
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN      13121/etcd
tcp        0      0 172.16.181.161:2380     0.0.0.0:*               LISTEN      13121/etcd
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN      13948/kube-apiserver
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      910/sshd
```

部署Controller Manager服务

```
[root@linux-node1 ssl]# vim /usr/lib/systemd/system/kube-controller-manager.service
---
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/GoogleCloudPlatform/kubernetes

[Service]
ExecStart=/opt/kubernetes/bin/kube-controller-manager \
  --address=127.0.0.1 \
  --master=http://127.0.0.1:8080 \
  --allocate-node-cidrs=true \
  --service-cluster-ip-range=10.1.0.0/16 \
  --cluster-cidr=10.2.0.0/16 \
  --cluster-name=kubernetes \
  --cluster-signing-cert-file=/opt/kubernetes/ssl/ca.pem \
  --cluster-signing-key-file=/opt/kubernetes/ssl/ca-key.pem \
  --service-account-private-key-file=/opt/kubernetes/ssl/ca-key.pem \
  --root-ca-file=/opt/kubernetes/ssl/ca.pem \
  --leader-elect=true \
  --v=2 \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/log

Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

[root@linux-node1 ssl]# systemctl daemon-reload
[root@linux-node1 ssl]# systemctl enable kube-controller-manager
[root@linux-node1 ssl]# systemctl start kube-controller-manager

[root@linux-node1 ssl]# systemctl status kube-controller-manager
```

部署Kubernetes Scheduler

```
[root@linux-node1 ssl]# vim /usr/lib/systemd/system/kube-scheduler.service
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/GoogleCloudPlatform/kubernetes

[Service]
ExecStart=/opt/kubernetes/bin/kube-scheduler \
  --address=127.0.0.1 \
  --master=http://127.0.0.1:8080 \
  --leader-elect=true \
  --v=2 \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/log

Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

[root@linux-node1 ssl]# systemctl daemon-reload
[root@linux-node1 ssl]# systemctl enable kube-scheduler
[root@linux-node1 ssl]# systemctl start kube-scheduler
[root@linux-node1 ssl]# systemctl status kube-scheduler
```

部署kubectl 命令行工具

```
[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/kubernetes/client/bin
[root@linux-node1 bin]# cp kubectl /opt/kubernetes/bin/
[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/ssl
[root@linux-node1 ssl]# vim admin-csr.json
{
  "CN": "admin",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "GuangZhou",
      "L": "ShenZhen",
      "O": "system:masters",
      "OU": "System"
    }
  ]
}

[root@linux-node1 ssl]# cfssl gencert -ca=/opt/kubernetes/ssl/ca.pem \
   -ca-key=/opt/kubernetes/ssl/ca-key.pem \
   -config=/opt/kubernetes/ssl/ca-config.json \
   -profile=kubernetes admin-csr.json | cfssljson -bare admin
   
[root@linux-node1 ssl]# cp admin*.pem /opt/kubernetes/ssl/

[root@linux-node1 ssl]# kubectl config set-cluster kubernetes \
   --certificate-authority=/opt/kubernetes/ssl/ca.pem \
   --embed-certs=true \
   --server=https://172.16.181.161:6443

[root@linux-node1 ssl]# kubectl config set-credentials admin \
   --client-certificate=/opt/kubernetes/ssl/admin.pem \
   --embed-certs=true \
   --client-key=/opt/kubernetes/ssl/admin-key.pem

[root@linux-node1 ssl]# kubectl config set-context kubernetes \
   --cluster=kubernetes \
   --user=admin

[root@linux-node1 ssl]# kubectl config use-context kubernetes

[root@linux-node1 ssl]# kubectl get cs
Please enter Username: admin
Please enter Password: *****
NAME                 STATUS    MESSAGE              ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-1               Healthy   {"health": "true"}
etcd-0               Healthy   {"health": "true"}
etcd-2               Healthy   {"health": "true"}
```

### Kubernetes Node节点部署
---

```
[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/kubernetes/server/bin/
[root@linux-node1 bin]# cp kubelet kube-proxy /opt/kubernetes/bin/
[root@linux-node1 bin]# scp kubelet kube-proxy linux-node2:/opt/kubernetes/bin/
[root@linux-node1 bin]# scp kubelet kube-proxy linux-node3:/opt/kubernetes/bin/

[root@linux-node1 bin]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/ssl/
[root@linux-node1 ssl]# kubectl create clusterrolebinding kubelet-bootstrap --clusterrole=system:node-bootstrapper --user=kubelet-bootstrap

[root@linux-node1 ssl]# kubectl config set-cluster kubernetes \
   --certificate-authority=/opt/kubernetes/ssl/ca.pem \
   --embed-certs=true \
   --server=https://172.16.181.161:6443 \
   --kubeconfig=bootstrap.kubeconfig

[root@linux-node1 ssl]# kubectl config set-credentials kubelet-bootstrap \
   --token=2cc9c96b39ff8cc4c96c1311475912c9 \
   --kubeconfig=bootstrap.kubeconfig

[root@linux-node1 ssl]# kubectl config set-context default \
   --cluster=kubernetes \
   --user=kubelet-bootstrap \
   --kubeconfig=bootstrap.kubeconfig

[root@linux-node1 ssl]# kubectl config use-context default --kubeconfig=bootstrap.kubeconfig

[root@linux-node1 ssl]# cp bootstrap.kubeconfig /opt/kubernetes/cfg/
[root@linux-node1 ssl]# scp bootstrap.kubeconfig linux-node2:/opt/kubernetes/cfg/
[root@linux-node1 ssl]# scp bootstrap.kubeconfig linux-node3:/opt/kubernetes/cfg/
```

到所有节点上执行

```
[root@linux-node1 ssl]# mkdir -p /etc/cni/net.d
[root@linux-node1 ssl]# vim /etc/cni/net.d/10-default.conf
{
        "name": "flannel",
        "type": "flannel",
        "delegate": {
            "bridge": "docker0",
            "isDefaultGateway": true,
            "mtu": 1400
        }
}

[root@linux-node1 ssl]# mkdir /var/lib/kubelet

[root@linux-node1 ssl]# vim /usr/lib/systemd/system/kubelet.service
[Unit]
Description=Kubernetes Kubelet
Documentation=https://github.com/GoogleCloudPlatform/kubernetes
After=docker.service
Requires=docker.service

[Service]
WorkingDirectory=/var/lib/kubelet
ExecStart=/opt/kubernetes/bin/kubelet \
  --address=172.16.181.162 \
  --hostname-override=172.16.181.162 \
  --pod-infra-container-image=mirrorgooglecontainers/pause-amd64:3.0 \
  --experimental-bootstrap-kubeconfig=/opt/kubernetes/cfg/bootstrap.kubeconfig \
  --kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig \
  --cert-dir=/opt/kubernetes/ssl \
  --network-plugin=cni \
  --cni-conf-dir=/etc/cni/net.d \
  --cni-bin-dir=/opt/kubernetes/bin/cni \
  --cluster-dns=10.1.0.2 \
  --cluster-domain=cluster.local. \
  --hairpin-mode hairpin-veth \
  --allow-privileged=true \
  --fail-swap-on=false \
  --logtostderr=true \
  --v=2 \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/log
Restart=on-failure
RestartSec=5

[root@linux-node1 ssl]# systemctl daemon-reload
[root@linux-node1 ssl]# systemctl enable kubelet
[root@linux-node1 ssl]# systemctl start kubelet

[root@linux-node1 ssl]# systemctl status kubelet  
```

到linux-node1上获取并approve

```
[root@linux-node1 ssl]# kubectl get csr
---
NAME                                                   AGE       REQUESTOR           CONDITION
node-csr-Ush7jFY8ImH6yyiW0GDdvg5_5mqmBhNrw874U7fyMqM   4m        kubelet-bootstrap   Pending

[root@linux-node1 ssl]# kubectl get csr|grep 'Pending' | awk 'NR>0{print $1}'| xargs kubectl certificate approve
---
certificatesigningrequest.certificates.k8s.io "node-csr-Ush7jFY8ImH6yyiW0GDdvg5_5mqmBhNrw874U7fyMqM" approved

[root@linux-node1 ssl]# kubectl get node
---
NAME             STATUS    ROLES     AGE       VERSION
172.16.181.162   Ready     <none>    12s       v1.10.1
```

部署Kubernetes Proxy

在所有节点上配置kube-proxy使用LVS

```
[root@linux-node1 ssl]# yum install -y ipvsadm ipset conntrack
```

在linux-node1上生成证书

```
[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/ssl/
[root@linux-node1 ssl]# vim kube-proxy-csr.json
---
{
  "CN": "system:kube-proxy",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "GuangZhou",
      "L": "ShenZhen",
      "O": "k8s",
      "OU": "System"
    }
  ]
}

[root@linux-node1 ssl]# cfssl gencert -ca=/opt/kubernetes/ssl/ca.pem \
   -ca-key=/opt/kubernetes/ssl/ca-key.pem \
   -config=/opt/kubernetes/ssl/ca-config.json \
   -profile=kubernetes  kube-proxy-csr.json | cfssljson -bare kube-proxy

[root@linux-node1 ssl]# cp kube-proxy*.pem /opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp kube-proxy*.pem linux-node2:/opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp kube-proxy*.pem linux-node3:/opt/kubernetes/ssl/

[root@linux-node1 ssl]# kubectl config set-cluster kubernetes \
   --certificate-authority=/opt/kubernetes/ssl/ca.pem \
   --embed-certs=true \
   --server=https://172.16.181.161:6443 \
   --kubeconfig=kube-proxy.kubeconfig

[root@linux-node1 ssl]# kubectl config set-credentials kube-proxy \
   --client-certificate=/opt/kubernetes/ssl/kube-proxy.pem \
   --client-key=/opt/kubernetes/ssl/kube-proxy-key.pem \
   --embed-certs=true \
   --kubeconfig=kube-proxy.kubeconfig

[root@linux-node1 ssl]# kubectl config set-context default \
   --cluster=kubernetes \
   --user=kube-proxy \
   --kubeconfig=kube-proxy.kubeconfig

[root@linux-node1 ssl]# kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig

[root@linux-node1 ssl]# cp kube-proxy.kubeconfig /opt/kubernetes/cfg/
[root@linux-node1 ssl]# scp kube-proxy.kubeconfig linux-node2:/opt/kubernetes/cfg/
[root@linux-node1 ssl]# scp kube-proxy.kubeconfig linux-node3:/opt/kubernetes/cfg/
```

在所有节点上创建服务

```
[root@linux-node1 ssl]# mkdir /var/lib/kube-proxy
[root@linux-node1 ssl]# vim /usr/lib/systemd/system/kube-proxy.service
---
[Unit]
Description=Kubernetes Kube-Proxy Server
Documentation=https://github.com/GoogleCloudPlatform/kubernetes
After=network.target

[Service]
WorkingDirectory=/var/lib/kube-proxy
ExecStart=/opt/kubernetes/bin/kube-proxy \
  --bind-address=172.16.181.162 \
  --hostname-override=172.16.181.162 \
  --kubeconfig=/opt/kubernetes/cfg/kube-proxy.kubeconfig \
--masquerade-all \
  --feature-gates=SupportIPVSProxyMode=true \
  --proxy-mode=ipvs \
  --ipvs-min-sync-period=5s \
  --ipvs-sync-period=5s \
  --ipvs-scheduler=rr \
  --logtostderr=true \
  --v=2 \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/log

Restart=on-failure
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target

[root@linux-node1 ssl]# systemctl daemon-reload
[root@linux-node1 ssl]# systemctl enable kube-proxy
[root@linux-node1 ssl]# systemctl start kube-proxy

[root@linux-node1 ssl]# systemctl status kube-proxy
[root@linux-node1 ssl]# ipvsadm -L -n
```

### Flannel网络部署和测试
---

Pod

```
Pause-amd64
User Container
User Container
```

RC

```
RC是K8S及群众保证Pod高可用的API对象。通过监控运行中的Pod来保证及群众运行指定书目的Pod副本。

指定的书目可以是多个也可以是1个；少于指定书目，RC就会启动运行新的Pod副本；多于指定书目，RC就会杀死多余的Pod副本。

即使在指定书目为1的情况下，通过RC运行Pod也比直接运行Pod更明智，因为RC也可以发挥它高可用的能力，保证永远有1个Pod在运行。
```

RS副本集

```
RS是新一代RC，提供同样的高可用能力，区别主要在于RS后来居上，能支持更多的匹配模式。副本集对象一般不单独使用，而是作为部署的理想状态参数使用。

是K8S 1.2中出现的概念，是RC的升级。一般和Deployment共同使用。
```

Deployment，管理应用的最核心的概念

```
Deployment表示用户对K8S集群的一次更新操作。Deployment是一个比RS应用模式更广的API对象。

可以是创建一个新的服务，更新一个新的服务，也可以是滚动升级一个服务。滚动升级一个服务，实际是创建一个新的RS，然后组件将新RS中副本数增加到理想状态，将旧RS中的副本数减少到0的复合操作。

这样一个复合操作用一个RS是不太好描述的，所以用一个更通用的Deployment来描述。
```

Service
 
 ```
 RC，RS和Deployment只是保证了支撑服务的POD的数量，但是没有解决如何访问这些服务的问题。一个Pod只是一个运行服务的实例，随时可能在一个节点上停止，在另一个节点以一个新的IP启动一个新的Pod，因此不能以确定的IP和端口号提供服务。
 
 要稳定地提供服务需要的服务发现和负载均衡能力。服务发现完成的工作，是针对客户端访问的服务，找到对应的后端服务实例。
 
 在K8S集群中，客户端要访问的服务就是Service对象。每个Service会对应一个集群内部有效的虚拟IP，集群内部通过虚拟IP访问一个服务。
 ```
 
IP
 
```
 Node IP: 节点设备的IP，如物理机，虚拟机等容器宿主的实际IP。
 Pod IP: Pod的IP地址，是根据docker0网格IP段进行分配的。
 Cluster IP: Service的IP，是一个虚拟IP，仅作用于Service对象，由K8S管理和分配，需要结合Service Port才能使用，单独的IP没有通信功能，集群外访问需要一些修改。
 在K8S集群内部，NodeIP，PodIP，ClusterIP的通信机制是由K8S制定的路由规则，不是IP路由。
```
 
Flannel网络
 
 ```
 Pod之间的通信
 
 Flannel使用ETCD，保证给某个Node分配的docker0的地址段不一样
 
 并且Flannel负责对这些IP地址进行封装和转发
 
 每个节点都需要启动Flannel
 ```
 
 安装配置Flannel
 
```
[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/ssl/
[root@linux-node1 ssl]# vim flanneld-csr.json
{
  "CN": "flanneld",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "GuangZhou",
      "L": "ShenZhen",
      "O": "k8s",
      "OU": "System"
    }
  ]
}

[root@linux-node1 ssl]# cfssl gencert -ca=/opt/kubernetes/ssl/ca.pem \
   -ca-key=/opt/kubernetes/ssl/ca-key.pem \
   -config=/opt/kubernetes/ssl/ca-config.json \
   -profile=kubernetes flanneld-csr.json | cfssljson -bare flanneld

[root@linux-node1 ssl]# cp flanneld*.pem /opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp flanneld*.pem linux-node2:/opt/kubernetes/ssl/
[root@linux-node1 ssl]# scp flanneld*.pem linux-node3:/opt/kubernetes/ssl/

[root@linux-node1 ssl]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/
[root@linux-node1 k8s-v1.10.1]# mkdir flannel-v0.10.0-linux-amd64
[root@linux-node1 k8s-v1.10.1]# cd flannel-v0.10.0-linux-amd64
[root@linux-node1 flannel-v0.10.0-linux-amd64]# tar xzf flannel-v0.10.0-linux-amd64.tar.gz
[root@linux-node1 flannel-v0.10.0-linux-amd64]# cp flanneld mk-docker-opts.sh /opt/kubernetes/bin/
[root@linux-node1 flannel-v0.10.0-linux-amd64]# scp flanneld mk-docker-opts.sh linux-node2:/opt/kubernetes/bin/
[root@linux-node1 flannel-v0.10.0-linux-amd64]# scp flanneld mk-docker-opts.sh linux-node3:/opt/kubernetes/bin/

[root@linux-node1 flannel-v0.10.0-linux-amd64]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/kubernetes/cluster/centos/node/bin/
[root@linux-node1 bin]# cp remove-docker0.sh /opt/kubernetes/bin/
[root@linux-node1 bin]# scp remove-docker0.sh linux-node2:/opt/kubernetes/bin/
[root@linux-node1 bin]# scp remove-docker0.sh linux-node3:/opt/kubernetes/bin/

[root@linux-node1 bin]# vim /opt/kubernetes/cfg/flannel
FLANNEL_ETCD="-etcd-endpoints=https://172.16.181.161:2379,https://172.16.181.162:2379,https://172.16.181.163:2379"
FLANNEL_ETCD_KEY="-etcd-prefix=/kubernetes/network"
FLANNEL_ETCD_CAFILE="--etcd-cafile=/opt/kubernetes/ssl/ca.pem"
FLANNEL_ETCD_CERTFILE="--etcd-certfile=/opt/kubernetes/ssl/flanneld.pem"
FLANNEL_ETCD_KEYFILE="--etcd-keyfile=/opt/kubernetes/ssl/flanneld-key.pem"

[root@linux-node1 bin]# scp /opt/kubernetes/cfg/flannel linux-node2:/opt/kubernetes/cfg/
[root@linux-node1 bin]# scp /opt/kubernetes/cfg/flannel linux-node3:/opt/kubernetes/cfg/

[root@linux-node1 bin]# vim /usr/lib/systemd/system/flannel.service
[Unit]
Description=Flanneld overlay address etcd agent
After=network.target
Before=docker.service

[Service]
EnvironmentFile=-/opt/kubernetes/cfg/flannel
ExecStartPre=/opt/kubernetes/bin/remove-docker0.sh
ExecStart=/opt/kubernetes/bin/flanneld ${FLANNEL_ETCD} ${FLANNEL_ETCD_KEY} ${FLANNEL_ETCD_CAFILE} ${FLANNEL_ETCD_CERTFILE} ${FLANNEL_ETCD_KEYFILE}
ExecStartPost=/opt/kubernetes/bin/mk-docker-opts.sh -d /run/flannel/docker

Type=notify

[Install]
WantedBy=multi-user.target
RequiredBy=docker.service

[root@linux-node1 bin]# scp /usr/lib/systemd/system/flannel.service linux-node2:/usr/lib/systemd/system/
[root@linux-node1 bin]# scp /usr/lib/systemd/system/flannel.service linux-node3:/usr/lib/systemd/system/

[root@linux-node1 bin]# cd /home/centos/k8s-v1.10.1-manual/k8s-v1.10.1/
[root@linux-node1 k8s-v1.10.1]#  mkdir /opt/kubernetes/bin/cni
[root@linux-node1 k8s-v1.10.1]#  tar zxf cni-plugins-amd64-v0.7.1.tgz -C /opt/kubernetes/bin/cni
[root@linux-node1 k8s-v1.10.1]#  ssh linux-node2 'mkdir /opt/kubernetes/bin/cni'
[root@linux-node1 k8s-v1.10.1]#  ssh linux-node3 'mkdir /opt/kubernetes/bin/cni'
[root@linux-node1 k8s-v1.10.1]#  scp -r /opt/kubernetes/bin/cni/* linux-node2:/opt/kubernetes/bin/cni/
[root@linux-node1 k8s-v1.10.1]#  scp -r /opt/kubernetes/bin/cni/* linux-node3:/opt/kubernetes/bin/cni/

[root@linux-node1 k8s-v1.10.1]#  /opt/kubernetes/bin/etcdctl --ca-file /opt/kubernetes/ssl/ca.pem --cert-file /opt/kubernetes/ssl/flanneld.pem --key-file /opt/kubernetes/ssl/flanneld-key.pem \
--no-sync -C https://172.16.181.161:2379,https://172.16.181.162:2379,https://172.16.181.163:2379 \
mk /kubernetes/network/config '{ "Network": "10.2.0.0/16", "Backend": { "Type": "vxlan", "VNI": 1 }}' >/dev/null 2>&1

[root@linux-node1 k8s-v1.10.1]#  systemctl daemon-reload
[root@linux-node1 k8s-v1.10.1]#  systemctl enable flannel
[root@linux-node1 k8s-v1.10.1]#  chmod +x /opt/kubernetes/bin/*
[root@linux-node1 k8s-v1.10.1]#  systemctl start flannel

[root@linux-node1 k8s-v1.10.1]#  systemctl status flannel

[root@linux-node1 k8s-v1.10.1]#  ifconfig | grep 10.[1-9]
inet 10.2.85.0  netmask 255.255.255.255  broadcast 0.0.0.0

[root@linux-node1 k8s-v1.10.1]#  cat /run/flannel/docker
DOCKER_OPT_BIP="--bip=10.2.85.1/24"
DOCKER_OPT_IPMASQ="--ip-masq=true"
DOCKER_OPT_MTU="--mtu=1450"
DOCKER_OPTS=" --bip=10.2.85.1/24 --ip-masq=true --mtu=1450"

[root@linux-node1 k8s-v1.10.1]#  vim /usr/lib/systemd/system/docker.service
[Unit] #在Unit下面修改After和增加Requires
After=network-online.target firewalld.service flannel.service
Wants=network-online.target
Requires=flannel.service

[Service] #增加EnvironmentFile=-/run/flannel/docker
Type=notify
EnvironmentFile=-/run/flannel/docker
ExecStart=/usr/bin/dockerd $DOCKER_OPTS

[root@linux-node1 k8s-v1.10.1]#  scp /usr/lib/systemd/system/docker.service linux-node2:/usr/lib/systemd/system/
[root@linux-node1 k8s-v1.10.1]#  scp /usr/lib/systemd/system/docker.service linux-node3:/usr/lib/systemd/system/

[root@linux-node1 k8s-v1.10.1]#  systemctl daemon-reload
[root@linux-node1 k8s-v1.10.1]#  systemctl restart docker
```

### 创建第一个K8S应用
---

见证奇迹的时刻到了

自动下载alpine镜像并创建Pod

```
[root@linux-node1 k8s-v1.10.1]#  cd
[root@linux-node1 ~]#  kubectl run net-test --image=alpine --replicas=2 sleep 360000
deployment.apps "net-test" created

[root@linux-node1 ~]#  kubectl get pod -o wide
NAME                        READY     STATUS    RESTARTS   AGE       IP           NODE
net-test-5767cb94df-5g6df   1/1       Running   0          1m        10.2.47.2    172.16.181.162
net-test-5767cb94df-vzlk8   1/1       Running   0          1m        10.2.102.2   172.16.181.163

[root@linux-node1 ~]#  ping 10.2.47.2
PING 10.2.47.2 (10.2.47.2) 56(84) bytes of data.
64 bytes from 10.2.47.2: icmp_seq=1 ttl=63 time=0.497 ms
64 bytes from 10.2.47.2: icmp_seq=2 ttl=63 time=0.433 ms
--- 10.2.47.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.433/0.465/0.497/0.032 ms
```

部署一个Nginx

```
[root@linux-node1 ~]#  mkdir -p /root/deploy/nginx
[root@linux-node1 ~]#  cd /root/deploy/nginx/
[root@linux-node1 nginx]# vim nginx-deployment.yaml
---
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

[root@linux-node1 nginx]# kubectl get deployment
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
net-test           2         2         2            2           13m
nginx-deployment   3         3         3            1           3m

[root@linux-node1 nginx]# kubectl describe deployment nginx-deployment

[root@linux-node1 nginx]# kubectl get pod -o wide
---
NAME                                READY     STATUS              RESTARTS   AGE       IP           NODE
net-test-5767cb94df-5g6df           1/1       Running             0          18m       10.2.47.2    172.16.181.162
net-test-5767cb94df-vzlk8           1/1       Running             0          18m       10.2.102.2   172.16.181.163
nginx-deployment-6c45fc49cb-cw6dn   0/1       ContainerCreating   0          8m        <none>       172.16.181.163
nginx-deployment-6c45fc49cb-mpjmq   1/1       Running             0          8m        10.2.47.3    172.16.181.162
nginx-deployment-6c45fc49cb-vmnfq   0/1       ContainerCreating   0          8m        <none>       172.16.181.163

[root@linux-node1 nginx]# kubectl describe pod/nginx-deployment-6c45fc49cb-cw6dn | tail -n 3
  Normal  Scheduled              6m    default-scheduler        Successfully assigned nginx-deployment-6c45fc49cb-cw6dn to 172.16.181.163
  Normal  SuccessfulMountVolume  6m    kubelet, 172.16.181.163  MountVolume.SetUp succeeded for volume "default-token-w5t7k"
  Normal  Pulling                6m    kubelet, 172.16.181.163  pulling image "nginx:1.13.12"

[root@linux-node1 nginx]# curl --head http://10.2.47.3
HTTP/1.1 200 OK
Server: nginx/1.13.12
Date: Thu, 02 Jan 2020 08:01:28 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 09 Apr 2018 16:01:09 GMT
Connection: keep-alive
ETag: "5acb8e45-264"
Accept-Ranges: bytes

[root@linux-node1 nginx]# kubectl set image deployment/nginx-deployment nginx=nginx:1.12.2 --record
deployment.apps "nginx-deployment" image updated

[root@linux-node1 nginx]# kubectl get pod -o wide
NAME                                READY     STATUS    RESTARTS   AGE       IP           NODE
net-test-5767cb94df-5g6df           1/1       Running   0          25m       10.2.47.2    172.16.181.162
net-test-5767cb94df-vzlk8           1/1       Running   0          25m       10.2.102.2   172.16.181.163
nginx-deployment-7498dc98f8-8xqr5   1/1       Running   0          1m        10.2.47.5    172.16.181.162
nginx-deployment-7498dc98f8-jx76k   1/1       Running   0          1m        10.2.102.5   172.16.181.163
nginx-deployment-7498dc98f8-q5vgv   1/1       Running   0          4m        10.2.47.4    172.16.181.162

[root@linux-node1 nginx]# kubectl describe pod/nginx-deployment-7498dc98f8-8xqr5 | tail -n 6
  ----    ------                 ----  ----                     -------
  Normal  Scheduled              1m    default-scheduler        Successfully assigned nginx-deployment-7498dc98f8-8xqr5 to 172.16.181.162
  Normal  SuccessfulMountVolume  1m    kubelet, 172.16.181.162  MountVolume.SetUp succeeded for volume "default-token-w5t7k"
  Normal  Pulled                 1m    kubelet, 172.16.181.162  Container image "nginx:1.12.2" already present on machine
  Normal  Created                1m    kubelet, 172.16.181.162  Created container
  Normal  Started                1m    kubelet, 172.16.181.162  Started container

K8S会自动滚动升级替换
[root@linux-node1 nginx]# kubectl get deployment -o wide
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINERS   IMAGES         SELECTOR
net-test           2         2         2            2           27m       net-test     alpine         run=net-test
nginx-deployment   3         3         3            3           18m       nginx        nginx:1.12.2   app=nginx

[root@linux-node1 nginx]# curl --head http://10.2.47.4
HTTP/1.1 200 OK
Server: nginx/1.12.2
Date: Thu, 02 Jan 2020 08:11:49 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 11 Jul 2017 13:29:18 GMT
Connection: keep-alive
ETag: "5964d2ae-264"
Accept-Ranges: bytes

record参数会记录相应的命令
[root@linux-node1 nginx]# kubectl rollout history deployment/nginx-deployment
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deployment/nginx-deployment nginx=nginx:1.12.2 --record=true

快速回滚到上一个版本
[root@linux-node1 nginx]# kubectl rollout undo deployment/nginx-deployment
deployment.apps "nginx-deployment"

[root@linux-node1 nginx]# kubectl get pod -o wide
NAME                                READY     STATUS        RESTARTS   AGE       IP           NODE
net-test-5767cb94df-5g6df           1/1       Running       0          31m       10.2.47.2    172.16.181.162
net-test-5767cb94df-vzlk8           1/1       Running       0          31m       10.2.102.2   172.16.181.163
nginx-deployment-6c45fc49cb-96lnh   1/1       Running       0          7s        10.2.102.7   172.16.181.163
nginx-deployment-6c45fc49cb-gcjxm   1/1       Running       0          13s       10.2.102.6   172.16.181.163
nginx-deployment-6c45fc49cb-sqpmz   1/1       Running       0          11s       10.2.47.6    172.16.181.162
nginx-deployment-7498dc98f8-jx76k   0/1       Terminating   0          8m        <none>       172.16.181.163
```

创建一个Nginx Service

```
[root@linux-node1 nginx]# cd /root/deploy/nginx/
[root@linux-node1 nginx]# vim nginx-service.yaml
kind: Service
apiVersion: v1
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

[root@linux-node1 nginx]# kubectl get svc
NAME            TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.1.0.1     <none>        443/TCP   1d
nginx-service   ClusterIP   10.1.92.0    <none>        80/TCP    15s

[root@linux-node1 nginx]# curl --head http://10.1.92.0
HTTP/1.1 200 OK
Server: nginx/1.13.12
Date: Thu, 02 Jan 2020 08:19:13 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 09 Apr 2018 16:01:09 GMT
Connection: keep-alive
ETag: "5acb8e45-264"
Accept-Ranges: bytes

[root@linux-node1 nginx]# ipvsadm -Ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.1.0.1:443 rr persistent 10800
  -> 172.16.181.161:6443          Masq    1      0          0
TCP  10.1.92.0:80 rr
  -> 10.2.47.6:80                 Masq    1      0          0
  -> 10.2.102.6:80                Masq    1      0          0
  -> 10.2.102.7:80                Masq    1      0          1

查看count数据变化确定负载均衡生效
[root@linux-node1 nginx]# ipvsadm -Ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.1.0.1:443 rr persistent 10800
  -> 172.16.181.161:6443          Masq    1      0          0
TCP  10.1.92.0:80 rr
  -> 10.2.47.6:80                 Masq    1      0          4
  -> 10.2.102.6:80                Masq    1      0          5
  -> 10.2.102.7:80                Masq    1      0          4

自动化扩容到5个副本
[root@linux-node1 nginx]# kubectl scale deployment nginx-deployment --replicas 5
deployment.extensions "nginx-deployment" scaled

[root@linux-node1 nginx]# kubectl get pod -o wide
NAME                                READY     STATUS              RESTARTS   AGE       IP           NODE
net-test-5767cb94df-5g6df           1/1       Running             0          47m       10.2.47.2    172.16.181.162
net-test-5767cb94df-vzlk8           1/1       Running             0          47m       10.2.102.2   172.16.181.163
nginx-deployment-6c45fc49cb-44p7k   0/1       ContainerCreating   0          24s       <none>       172.16.181.161
nginx-deployment-6c45fc49cb-96lnh   1/1       Running             0          15m       10.2.102.7   172.16.181.163
nginx-deployment-6c45fc49cb-b6vkb   0/1       ContainerCreating   0          24s       <none>       172.16.181.161
nginx-deployment-6c45fc49cb-gcjxm   1/1       Running             0          15m       10.2.102.6   172.16.181.163
nginx-deployment-6c45fc49cb-sqpmz   1/1       Running             0          15m       10.2.47.6    172.16.181.162
```

### CoreDNS和Dashboard部署
---

创建CoreDNS服务

```
[root@linux-node1 nginx]# mkdir /root/deploy/coredns
[root@linux-node1 nginx]# cd /root/deploy/coredns/
[root@linux-node1 coredns]# vim coredns.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: coredns
  namespace: kube-system
  labels:
      kubernetes.io/cluster-service: "true"
      addonmanager.kubernetes.io/mode: Reconcile
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
    addonmanager.kubernetes.io/mode: Reconcile
  name: system:coredns
rules:
- apiGroups:
  - ""
  resources:
  - endpoints
  - services
  - pods
  - namespaces
  verbs:
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
    addonmanager.kubernetes.io/mode: EnsureExists
  name: system:coredns
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:coredns
subjects:
- kind: ServiceAccount
  name: coredns
  namespace: kube-system
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns
  namespace: kube-system
  labels:
      addonmanager.kubernetes.io/mode: EnsureExists
data:
  Corefile: |
    .:53 {
        errors
        health
        kubernetes cluster.local. in-addr.arpa ip6.arpa {
            pods insecure
            upstream
            fallthrough in-addr.arpa ip6.arpa
        }
        prometheus :9153
        proxy . /etc/resolv.conf
        cache 30
    }
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: coredns
  namespace: kube-system
  labels:
    k8s-app: coredns
    kubernetes.io/cluster-service: "true"
    addonmanager.kubernetes.io/mode: Reconcile
    kubernetes.io/name: "CoreDNS"
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  selector:
    matchLabels:
      k8s-app: coredns
  template:
    metadata:
      labels:
        k8s-app: coredns
    spec:
      serviceAccountName: coredns
      tolerations:
        - key: node-role.kubernetes.io/master
          effect: NoSchedule
        - key: "CriticalAddonsOnly"
          operator: "Exists"
      containers:
      - name: coredns
        image: coredns/coredns:1.0.6
        imagePullPolicy: IfNotPresent
        resources:
          limits:
            memory: 170Mi
          requests:
            cpu: 100m
            memory: 70Mi
        args: [ "-conf", "/etc/coredns/Corefile" ]
        volumeMounts:
        - name: config-volume
          mountPath: /etc/coredns
        ports:
        - containerPort: 53
          name: dns
          protocol: UDP
        - containerPort: 53
          name: dns-tcp
          protocol: TCP
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 60
          timeoutSeconds: 5
          successThreshold: 1
          failureThreshold: 5
      dnsPolicy: Default
      volumes:
        - name: config-volume
          configMap:
            name: coredns
            items:
            - key: Corefile
              path: Corefile
---
apiVersion: v1
kind: Service
metadata:
  name: coredns
  namespace: kube-system
  labels:
    k8s-app: coredns
    kubernetes.io/cluster-service: "true"
    addonmanager.kubernetes.io/mode: Reconcile
    kubernetes.io/name: "CoreDNS"
spec:
  selector:
    k8s-app: coredns
  clusterIP: 10.1.0.2
  ports:
  - name: dns
    port: 53
    protocol: UDP
  - name: dns-tcp
    port: 53
    protocol: TCP

[root@linux-node1 coredns]# kubectl create -f coredns.yaml
serviceaccount "coredns" created
clusterrole.rbac.authorization.k8s.io "system:coredns" created
clusterrolebinding.rbac.authorization.k8s.io "system:coredns" created
configmap "coredns" created
deployment.extensions "coredns" created
service "coredns" created

[root@linux-node1 coredns]# kubectl get deployment -n kube-system
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
coredns   2         2         2            2           1m

[root@linux-node1 coredns]# kubectl get service -n kube-system
NAME      TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
coredns   ClusterIP   10.1.0.2     <none>        53/UDP,53/TCP   1m

[root@linux-node1 coredns]# ipvsadm -Ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.1.0.1:443 rr persistent 10800
  -> 172.16.181.161:6443          Masq    1      1          0
TCP  10.1.0.2:53 rr
  -> 10.2.85.4:53                 Masq    1      0          0
  -> 10.2.102.8:53                Masq    1      0          0
TCP  10.1.92.0:80 rr
  -> 10.2.47.6:80                 Masq    1      0          0
  -> 10.2.102.6:80                Masq    1      0          0
UDP  10.1.0.2:53 rr
  -> 10.2.85.4:53                 Masq    1      0          0
  -> 10.2.102.8:53                Masq    1      0          0

DNS请求会转发到后面两个Pod中
[root@linux-node1 coredns]# kubectl get pod -n kube-system
NAME                       READY     STATUS    RESTARTS   AGE
coredns-77c989547b-g6ttq   1/1       Running   0          3m
coredns-77c989547b-gnxr8   1/1       Running   0          3m

测试DNS解析
[root@linux-node1 coredns]# kubectl run dns-test --rm -it --image=alpine /bin/sh
If you don't see a command prompt, try pressing enter.
/ # ping www.baidu.com
PING www.baidu.com (14.215.177.39): 56 data bytes
64 bytes from 14.215.177.39: seq=0 ttl=127 time=10.944 ms
--- www.baidu.com ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 10.944/10.944/10.944 ms

/ # nslookup nginx-service 127.0.0.1
Server:    127.0.0.1
Address 1: 127.0.0.1 localhost

Name:      nginx-service
Address 1: 10.1.92.0 nginx-service.default.svc.cluster.local

/ # ping nginx-service
PING nginx-service (10.1.92.0): 56 data bytes
64 bytes from 10.1.92.0: seq=0 ttl=64 time=0.159 ms
--- nginx-service ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.159/0.159/0.159 ms

/ # exit
Session ended, resume using 'kubectl attach dns-test-c6c485c47-lsggz -c dns-test -i -t' command when the pod is running
```

创建Dashboard

```
[root@linux-node1 coredns]# mkdir /root/deploy/dashboard
[root@linux-node1 coredns]# cd /root/deploy/dashboard/

[root@linux-node1 dashboard]# vim admin-user-sa-rbac.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system

[root@linux-node1 dashboard]# vim kubernetes-dashboard.yaml
# Copyright 2017 The Kubernetes Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Configuration to deploy release version of the Dashboard UI compatible with
# Kubernetes 1.8.
#
# Example usage: kubectl create -f <this_file>

# ------------------- Dashboard Secret ------------------- #

apiVersion: v1
kind: Secret
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-certs
  namespace: kube-system
type: Opaque

---
# ------------------- Dashboard Service Account ------------------- #

apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system

---
# ------------------- Dashboard Role & Role Binding ------------------- #

kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: kubernetes-dashboard-minimal
  namespace: kube-system
rules:
  # Allow Dashboard to create 'kubernetes-dashboard-key-holder' secret.
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["create"]
  # Allow Dashboard to create 'kubernetes-dashboard-settings' config map.
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
  # Allow Dashboard to get, update and delete Dashboard exclusive secrets.
- apiGroups: [""]
  resources: ["secrets"]
  resourceNames: ["kubernetes-dashboard-key-holder", "kubernetes-dashboard-certs"]
  verbs: ["get", "update", "delete"]
  # Allow Dashboard to get and update 'kubernetes-dashboard-settings' config map.
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["kubernetes-dashboard-settings"]
  verbs: ["get", "update"]
  # Allow Dashboard to get metrics from heapster.
- apiGroups: [""]
  resources: ["services"]
  resourceNames: ["heapster"]
  verbs: ["proxy"]
- apiGroups: [""]
  resources: ["services/proxy"]
  resourceNames: ["heapster", "http:heapster:", "https:heapster:"]
  verbs: ["get"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: kubernetes-dashboard-minimal
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: kubernetes-dashboard-minimal
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system

---
# ------------------- Dashboard Deployment ------------------- #

kind: Deployment
apiVersion: apps/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: kubernetes-dashboard
  template:
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
    spec:
      containers:
      - name: kubernetes-dashboard
        #image: k8s.gcr.io/kubernetes-dashboard-amd64:v1.8.3
        image: mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.8.3
        ports:
        - containerPort: 8443
          protocol: TCP
        args:
          - --auto-generate-certificates
          # Uncomment the following line to manually specify Kubernetes API server Host
          # If not specified, Dashboard will attempt to auto discover the API server and connect
          # to it. Uncomment only if the default does not work.
          # - --apiserver-host=http://my-address:port
        volumeMounts:
        - name: kubernetes-dashboard-certs
          mountPath: /certs
          # Create on-disk volume to store exec logs
        - mountPath: /tmp
          name: tmp-volume
        livenessProbe:
          httpGet:
            scheme: HTTPS
            path: /
            port: 8443
          initialDelaySeconds: 30
          timeoutSeconds: 30
      volumes:
      - name: kubernetes-dashboard-certs
        secret:
          secretName: kubernetes-dashboard-certs
      - name: tmp-volume
        emptyDir: {}
      serviceAccountName: kubernetes-dashboard
      # Comment the following tolerations if Dashboard must not be deployed on master
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule

---
# ------------------- Dashboard Service ------------------- #

kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
    kubernetes.io/cluster-service: "true"
    addonmanager.kubernetes.io/mode: Reconcile
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 30001
  selector:
    k8s-app: kubernetes-dashboard
  type: NodePort

vim ui-admin-rbac.yaml
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: ui-admin
rules:
- apiGroups:
  - ""
  resources:
  - services
  - services/proxy
  verbs:
  - '*'

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: ui-admin-binding
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: ui-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: admin

vim ui-read-rbac.yaml
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: ui-read
rules:
- apiGroups:
  - ""
  resources:
  - services
  - services/proxy
  verbs:
  - get
  - list
  - watch

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: ui-read-binding
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: ui-read
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: readonly

[root@linux-node1 dashboard]# ls -l
-rw-r--r-- 1 root root  355 Jan  2 17:01 admin-user-sa-rbac.yaml
-rw-r--r-- 1 root root 4940 Jan  2 17:02 kubernetes-dashboard.yaml
-rw-r--r-- 1 root root  458 Jan  2 17:03 ui-admin-rbac.yaml
-rw-r--r-- 1 root root  477 Jan  2 17:04 ui-read-rbac.yaml

[root@linux-node1 dashboard]# kubectl create -f .
serviceaccount "admin-user" created
clusterrolebinding.rbac.authorization.k8s.io "admin-user" created
secret "kubernetes-dashboard-certs" created
serviceaccount "kubernetes-dashboard" created
role.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
rolebinding.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
deployment.apps "kubernetes-dashboard" created
service "kubernetes-dashboard" created
clusterrole.rbac.authorization.k8s.io "ui-admin" created
rolebinding.rbac.authorization.k8s.io "ui-admin-binding" created
clusterrole.rbac.authorization.k8s.io "ui-read" created
rolebinding.rbac.authorization.k8s.io "ui-read-binding" created

[root@linux-node1 dashboard]# kubectl get pod -n kube-system
NAME                                    READY     STATUS    RESTARTS   AGE
coredns-77c989547b-g6ttq                1/1       Running   0          24m
coredns-77c989547b-gnxr8                1/1       Running   0          24m
kubernetes-dashboard-66c9d98865-czvqw   1/1       Running   0          1m

[root@linux-node1 dashboard]# kubectl get service -n kube-system
NAME                   TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)         AGE
coredns                ClusterIP   10.1.0.2      <none>        53/UDP,53/TCP   24m
kubernetes-dashboard   NodePort    10.1.93.247   <none>        443:30001/TCP   1m

访问任意一台服务器的NodeIP的30001的https端口
获取一个token
[root@linux-node1 dashboard]# kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
Name:         admin-user-token-q6knp
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name=admin-user
              kubernetes.io/service-account.uid=059127e0-2d3f-11ea-9dee-000c298407a7

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1367 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLXE2a25wIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIwNTkxMjdlMC0yZDNmLTExZWEtOWRlZS0wMDBjMjk4NDA3YTciLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.5hdbp6oHMGunH6ULjnaqLu9Fzr3bJIF5-TwdyMnCX9JvlYJx1DVyGbF2MSj3RINfqp_HOlmTfAZETsKs6Qk8gIUIThR8XsZcwzWOP6uwSy18zZEN_QGPVIo3V8rueVX_Y8Z7BRosWVQOic2icOBuwgbAOSK7ZzKKybELg6eKCb5Uv0B-shgm7yPXUuop9pS8VpghgAtIKa2xo2S9kKBUv9mjUrTR8TvZkW4sZMNZkOZMGMtoiqvIqAa5XFtdy2mRhEzOTIGdAVkuGgWUIKxRFhzEBqDgVeX4AChivW69xu90S8i01nEWAUT5SoHwMfmTJjLZh5tvRVeptfpq2DQshA
```

复制token然后登陆即可查看集群相关信息
![K8S Dashboard](https://raw.githubusercontent.com/mcsrainbow/notes/master/k8s/images/k8s_dashboard_01.jpg)

![K8S Dashboard](https://raw.githubusercontent.com/mcsrainbow/notes/master/k8s/images/k8s_dashboard_02.jpg)
