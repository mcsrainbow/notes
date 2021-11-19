## Kubernetes cluster quick install via kubeadm

### Prepare the EC2 instances

```
OS: CentOS 7.9 x86_64
kubeadm01: 172.31.33.180 
kubeadm02: 172.31.47.104 
kubeadm03: 172.31.38.119
```

### Upgrade kernel and packages on All Nodes 

```
[centos@kubeadm01 ~]$ sudo yum -y update
```


### Configure /etc/hostname on All Nodes

```
[centos@kubeadm01 ~]$ cat > /etc/hostname <<EOF
kubeadm01
EOF
[centos@kubeadm01 ~]$ sudo hostname kubeadm01

[centos@kubeadm02 ~]$ cat > /etc/hostname <<EOF
kubeadm02
EOF
[centos@kubeadm02 ~]$ sudo hostname kubeadm02

[centos@kubeadm01 ~]$ cat > /etc/hostname <<EOF
kubeadm03
EOF
[centos@kubeadm03 ~]$ sudo hostname kubeadm03
```

### Configure /etc/hosts on All Nodes

```
[centos@kubeadm01 ~]$ cat >> /etc/hosts <<EOF
172.31.33.180 kubeadm01
172.31.47.104 kubeadm02
172.31.38.119 kubeadm03
EOF
```
