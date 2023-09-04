# Docker Notes
![Docker logo](https://www.docker.com/wp-content/uploads/2021/10/horizontal-logo-monochromatic-white-730x188.png)

**参考资料：**[Docker 中文文档](https://docs.docker-cn.com/get-started/)《Docker 技术入门与实战》

## 20180829
### 安装配置Docker
**参考资料：** [在 CentOS 上安装 Docker CE](https://docs.docker-cn.com/engine/installation/linux/docker-ce/centos/)

操作系统环境

```
[root@localhost ~]# cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)
```

将用户添加到adm组并赋予adm组NOPASS的sudo权限

```
[root@localhost ~]# useradd dong -g adm
[root@localhost ~]# id dong
uid=1000(dong) gid=4(adm) groups=4(adm)
[root@localhost ~]# sudo grep adm /etc/sudoers
%adm	ALL=(ALL)	NOPASSWD: ALL
```

修改CentOS-Base.repo和epel.repo，使用国内YUM仓库

```
[dong@localhost ~]$ cat /etc/yum.repos.d/CentOS-Base.repo
[base]
name=CentOS-$releasever - Base
baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-$releasever - Updates
baseurl=http://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever - Extras
baseurl=http://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[centosplus]
name=CentOS-$releasever - Plus
baseurl=http://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

```
[dong@localhost ~]$ cat /etc/yum.repos.d/epel.repo
[epel]
name=Extra Packages for Enterprise Linux 7 - $basearch
baseurl=http://mirrors.aliyun.com/epel/7/$basearch
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7

[epel-debuginfo]
name=Extra Packages for Enterprise Linux 7 - $basearch - Debug
baseurl=http://mirrors.aliyun.com/epel/7/$basearch/debug
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=1

[epel-source]
name=Extra Packages for Enterprise Linux 7 - $basearch - Source
baseurl=http://mirrors.aliyun.com/epel/7/SRPMS
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=1
```

配置firewalld，使容器之间能够正常通信

```
[dong@localhost ~]$ firewall-cmd --permanent --zone=trusted --add-interface=docker0
[dong@localhost ~]$ firewall-cmd --permanent --zone=trusted --add-port=4243/tcp
```

优化sshd相关参数

```
[dong@localhost ~]$ sudo vim /etc/ssh/sshd_config
UseDNS no
[dong@localhost ~]$ sudo systemctl restart sshd
```

安装所需的软件包

```
[dong@localhost ~]$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

配置docker-ce.repo，使用国内YUM仓库

```
[dong@localhost ~]$ sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

安装指定版本的Docker CE

```
[dong@localhost ~]$ sudo yum makecache fast
[dong@localhost ~]$ sudo yum install docker-ce-18.06.0.ce-3.el7.x86_64
```

配置Docker镜像加速

```
[dong@localhost ~]$ sudo cat /etc/docker/daemon.json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

启动Docker

```
[dong@localhost ~]$ sudo systemctl start docker
[dong@localhost ~]$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2018-08-13 04:32:22 EDT; 5s ago
     Docs: https://docs.docker.com
 Main PID: 3073 (dockerd)
    Tasks: 18
   Memory: 43.7M
   CGroup: /system.slice/docker.service
           ├─3073 /usr/bin/dockerd
           └─3079 docker-containerd --config /var/run/docker/containerd/containerd.toml

Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.124429121-04:00" level=info msg="pickfirstBalancer: HandleSubConnStateChange: 0xc42017f...odule=grpc
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.124592709-04:00" level=info msg="pickfirstBalancer: HandleSubConnStateChange: 0xc42017f...odule=grpc
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.124609852-04:00" level=info msg="Loading containers: start."
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.450309512-04:00" level=info msg="Default bridge (docker0) is assigned with an IP addres...P address"
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.723104438-04:00" level=info msg="Loading containers: done."
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.755741407-04:00" level=info msg="Docker daemon" commit=0ffa825 graphdriver(s)=overlay2 ...18.06.0-ce
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.756191861-04:00" level=info msg="Daemon has completed initialization"
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.764278382-04:00" level=warning msg="Could not register builder git source: failed to fi... in $PATH"
Aug 13 04:32:22 localhost.localdomain dockerd[3073]: time="2018-08-13T04:32:22.781490149-04:00" level=info msg="API listen on /var/run/docker.sock"
Aug 13 04:32:22 localhost.localdomain systemd[1]: Started Docker Application Container Engine.
```

测试并运行hello-world

```
[dong@localhost ~]$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
9db2ca6ccae0: Pull complete
Digest: sha256:4b8ff392a12ed9ea17784bd3c9a8b1fa3299cac44aca35a85c90c5e3c7afacdc
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

### Docker 镜像（image）与容器（container）的基本操作

测试获取centos镜像

```
[dong@localhost ~]$ sudo docker pull centos
Using default tag: latest
latest: Pulling from library/centos
256b176beaff: Pull complete
Digest: sha256:6f6d986d425aeabdc3a02cb61c02abb2e78e57357e92417d6d58332856024faf
Status: Downloaded newer image for centos:latest
```

查看本地镜像

```
[dong@localhost ~]$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              5182e96772bf        6 days ago          200MB
hello-world         latest              2cb0d9787c4d        4 weeks ago         1.85kB
```

配置当前用户并赋予docker用户组权限

```
[dong@localhost ~]$ sudo usermod -aG docker $(whoami)
You will need to log out and back in as the same user to enable this change.
```

登陆到公网DockerHub

```
[dong@localhost ~]$ docker login -u mcsrainbow
Password:
WARNING! Your password will be stored unencrypted in /home/dong/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
```

Pull指定版本的Ubuntu镜像

```
[dong@localhost ~]$ docker pull ubuntu:14.04
14.04: Pulling from library/ubuntu
72c01b436656: Pull complete
944f9cf63457: Pull complete
6856614e8780: Pull complete
9d0e4b6507f2: Pull complete
c00913272b9b: Pull complete
Digest: sha256:d6b8e25d0f3073bad48fda3f28d01ac0e8bd0e2a6d034c7f462619591a72c791
Status: Downloaded newer image for ubuntu:14.04

[dong@localhost ~]$ docker pull ubuntu:16.04
16.04: Pulling from library/ubuntu
3b37166ec614: Pull complete
ba077e1ddb3a: Pull complete
34c83d2bc656: Pull complete
84b69b6e4743: Pull complete
0f72e97e1f61: Pull complete
Digest: sha256:a218d8dacc99e49cbdc5862d5c6ee105eb40d4e87bec2e4dac37361c30171ce0
Status: Downloaded newer image for ubuntu:16.04
```

搜索收藏数大于100的centos镜像

```
[dong@localhost ~]$ docker search -s 100 centos
Flag --stars has been deprecated, use --filter=stars=3 instead
NAME                      DESCRIPTION                     STARS               OFFICIAL            AUTOMATED
centos                    The official build of CentOS.   4635                [OK]
ansible/centos7-ansible   Ansible on Centos7              115                                     [OK]
```

列出指定镜像的所有tag

```
[dong@localhost ~]$ vim .bashrc
# Simple script that will display docker repository tags.
# Usage:
#   $ ds ubuntu centos
function ds() {
    for Repo in $* ; do
        curl -s -S "https://registry.hub.docker.com/v2/repositories/library/${Repo}/tags/?page_size=1024" | \
        sed -e 's/,/,\n/g' -e 's/\[/\[\n/g' | \
        grep '"name"' | \
        awk -F\" '{print $4;}' | \
        sort -fu | \
        sed -e "s/^/${Repo}:/"
    done
}

[dong@localhost ~]$ source .bashrc
[dong@localhost ~]$ ds centos
centos:5
centos:5.11
centos:6
centos:6.10
centos:6.6
centos:6.7
centos:6.8
centos:6.9
centos:7
centos:7.0.1406
centos:7.1.1503
centos:7.2.1511
centos:7.3.1611
centos:7.4.1708
centos:7.5.1804
centos:centos5
centos:centos5.11
centos:centos6
centos:centos6.10
centos:centos6.6
centos:centos6.7
centos:centos6.8
centos:centos6.9
centos:centos7
centos:centos7.0.1406
centos:centos7.1.1503
centos:centos7.2.1511
centos:centos7.3.1611
centos:centos7.4.1708
centos:centos7.5.1804
centos:latest
```

Pull指定版本的CentOS镜像

```
[dong@localhost ~]$ docker pull centos:7.5.1804
7.5.1804: Pulling from library/centos
Digest: sha256:7fa8d6e1b61a3f9fc1413be9c2f8a8a84dab8855cdb5c467d144ff171155fd19
Status: Downloaded newer image for centos:7.5.1804
```

添加自定义tag

```
[dong@localhost ~]$ docker tag centos:latest mycentos:latest
[dong@localhost ~]$ docker images
REPOSITORY                       TAG                 IMAGE ID            CREATED                  SIZE
ubuntu                           16.04               52b10959e8aa        Less than a second ago   115MB
ubuntu                           14.04               8789038981bc        Less than a second ago   188MB
centos                           7.5.1804            fdf13fa91c6e        6 days ago               200MB
centos                           latest              5182e96772bf        6 days ago               200MB
mycentos                         latest              5182e96772bf        6 days ago               200MB
hello-world                      latest              2cb0d9787c4d        4 weeks ago              1.85kB
```

使用标签删除镜像

```
[dong@localhost ~]$ docker rmi mycentos:latest
Untagged: mycentos:latest

[dong@localhost ~]$ docker rmi centos:latest
Untagged: centos:latest
Untagged: centos@sha256:6f6d986d425aeabdc3a02cb61c02abb2e78e57357e92417d6d58332856024faf
Deleted: sha256:5182e96772bf11f4b912658e265dfe0db8bd314475443b6434ea708784192892
Deleted: sha256:1d31b5806ba40b5f67bde96f18a181668348934a44c9253b420d5f04cfb4e37a
```

使用inspect命令查看详细信息

```
[dong@localhost ~]$ docker inspect centos:7.5.1804
[
    {
        "Id": "sha256:fdf13fa91c6e0445e51dfd26443aa4bcd561fd40a7c5e85718c4df8e67bcc514",
        "RepoTags": [
            "centos:7.5.1804",
            "centos:centos7.5.1804"
        ],
        "RepoDigests": [
            "centos@sha256:7fa8d6e1b61a3f9fc1413be9c2f8a8a84dab8855cdb5c467d144ff171155fd19"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2018-08-06T19:23:48.587844012Z",
        "Container": "6cd823a6456b8e564d69d56791c2b25393930e517d098a59643a4541d1bd7c41",
        "ContainerConfig": {
            "Hostname": "6cd823a6456b",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/bin/bash\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:891aea67a5d920d423028519231740e3f589de3fe72ceaf8a895706445eec818",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.schema-version": "= 1.0     org.label-schema.name=CentOS Base Image     org.label-schema.vendor=CentOS     org.label-schema.license=GPLv2     org.label-schema.build-date=20180531"
            }
        },
        "DockerVersion": "17.06.2-ce",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:891aea67a5d920d423028519231740e3f589de3fe72ceaf8a895706445eec818",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.schema-version": "= 1.0     org.label-schema.name=CentOS Base Image     org.label-schema.vendor=CentOS     org.label-schema.license=GPLv2     org.label-schema.build-date=20180531"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 199678471,
        "VirtualSize": 199678471,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/d114306f83a5a6be98211987bc90472d12c01031cab9251e8e3bb4a3545c514a/merged",
                "UpperDir": "/var/lib/docker/overlay2/d114306f83a5a6be98211987bc90472d12c01031cab9251e8e3bb4a3545c514a/diff",
                "WorkDir": "/var/lib/docker/overlay2/d114306f83a5a6be98211987bc90472d12c01031cab9251e8e3bb4a3545c514a/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:bcc97fbfc9e1a709f0eb78c1da59caeb65f43dc32cd5deeb12b8c1784e5b8237"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

使用history查看镜像历史

```
[dong@localhost ~]$ docker history centos:7.5.1804
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
fdf13fa91c6e        6 days ago          /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           6 days ago          /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>           6 days ago          /bin/sh -c #(nop) ADD file:8f4b3be0c1427b158…   200MB
```

导出和导入centos镜像

```
[dong@localhost ~]$ mkdir backups
[dong@localhost ~]$ docker save -o backups/centos_7.5.1804.tar centos:7.5.1804
[dong@localhost ~]$ ls -lh backups/
total 199M
-rw-------. 1 dong adm 199M Aug 13 09:46 centos_7.5.1804.tar

[dong@localhost ~]$ docker load < backups/centos_7.5.1804.tar
Loaded image: centos:7.5.1804

[dong@localhost ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED                  SIZE
ubuntu              16.04               52b10959e8aa        Less than a second ago   115MB
ubuntu              14.04               8789038981bc        Less than a second ago   188MB
centos              7.5.1804            fdf13fa91c6e        6 days ago               200MB
hello-world         latest              2cb0d9787c4d        4 weeks ago              1.85kB
```

启动一个alpine容器并登陆，进行变更后提交生成一个新的image

```
[dong@localhost ~]$ docker pull alpine:3.8
3.8: Pulling from library/alpine
8e3ba11ec2a2: Pull complete
Digest: sha256:7043076348bf5040220df6ad703798fd8593a0918d06d3ce30c6c93be117e430
Status: Downloaded newer image for alpine:3.8

[dong@localhost ~]$ docker run -it alpine:3.8 ash
/ # echo "docker.notes" > /root/docker.notes.txt
/ # exit

[dong@localhost ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS               NAMES
68eaccf07373        alpine:3.8          "ash"               About a minute ago   Exited (0) 21 seconds ago                       dreamy_kalam

[dong@localhost ~]$ docker commit -m "Added /root/docker.notes.txt" -a "Dong Guo" 68eaccf07373 alpine:notes.0.1
sha256:04c2fb8571c63b566a26a99e614b3cd6d9213bb21ec81c8c5d6506728ea3aa4d
```

将本地镜像与镜像库中的镜像仓库相关联，并上传镜像（默认指向到Docker Hub官方仓库）

```
[dong@localhost ~]$ docker login
Authenticating with existing credentials...
Login Succeeded

[dong@localhost ~]$ docker push mcsrainbow/notes:alpine.notes.0.1
The push refers to repository [docker.io/mcsrainbow/notes]
fa1d06935e77: Pushed
73046094a9b8: Mounted from library/alpine
alpine.notes.0.1: digest: sha256:92ef4179f8d2405a6efc53fa68818fcdc18ef0ddbad4d90e368461b957485445 size: 735
```

启动刚刚创建的容器

```
[dong@localhost ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
68eaccf07373        alpine:3.8          "ash"               5 minutes ago       Exited (0) 4 minutes ago                       dreamy_kalam

[dong@localhost ~]$ docker start 68ea
68ea

[dong@localhost ~]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
68eaccf07373        alpine:3.8          "ash"               5 minutes ago       Up 10 seconds                           dreamy_kalam
```

使用Docker exec与nsenter进入刚刚创建的容器

```
[dong@localhost ~]$ docker exec -it 68eaccf07373 ash
/ # cd /root
~ # ls
docker.notes.txt
~ # cat docker.notes.txt
docker.notes
~ # exit

[dong@localhost ~]$ PID=$(docker inspect --format "{{ .State.Pid }}" 68eaccf07373)
[dong@localhost ~]$ echo $PID
6475
[dong@localhost ~]$ sudo nsenter --target $PID --mount --uts --ipc --net --pid ash
/ # cat /root/docker.notes.txt
docker.notes
/ # exit
```

终止刚刚创建的容器

```
[dong@localhost ~]$ docker stop 68ea
68ea

[dong@localhost ~]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

[dong@localhost ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                       PORTS               NAMES
68eaccf07373        alpine:3.8          "ash"               13 minutes ago      Exited (137) 3 minutes ago                       dreamy_kalam
```

导出容器

```
[dong@localhost ~]$ docker export -o alpine_docker.notes.tar 68ea
[dong@localhost ~]$ ls -lh alpine_docker.notes.tar
-rw-------. 1 dong adm 4.5M Aug 13 11:11 alpine_docker.notes.tar
```

删除容器

```
[dong@localhost ~]$ docker rm 68ea
68ea
```

导入容器

```
[dong@localhost ~]$ docker import alpine_docker.notes.tar alpine:notes.imported.3.8
sha256:a22bbdf8ccd75f59a5f6dff212cc34beda1b3bb48a6d3dae3d339b70c3ba0849
[dong@localhost ~]$ docker images
REPOSITORY          TAG                  IMAGE ID            CREATED                  SIZE
ubuntu              16.04                52b10959e8aa        Less than a second ago   115MB
ubuntu              14.04                8789038981bc        Less than a second ago   188MB
alpine              notes.imported.3.8   a22bbdf8ccd7        4 seconds ago            4.41MB
alpine              notes.0.1            04c2fb8571c6        26 minutes ago           4.41MB
mcsrainbow/notes    alpine.notes.0.1     04c2fb8571c6        26 minutes ago           4.41MB
centos              7.5.1804             fdf13fa91c6e        6 days ago               200MB
hello-world         latest               2cb0d9787c4d        4 weeks ago              1.85kB
alpine              3.8                  11cd0b38bc3c        5 weeks ago              4.41MB
```

### Docker 仓库（repository）的基本操作

获取本地IP地址

```
[dong@localhost ~]$ ip a | grep -a ens33 | grep inet | awk '{print $2}' | cut -d/ -f1
172.16.180.133
```

将本地仓库的监听地址和端口设置为可信任

```
[dong@localhost ~]$ sudo vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://registry.docker-cn.com"],
  "insecure-registries":["172.16.180.133:5000"]
}

[dong@localhost ~]$ sudo systemctl restart docker
```

拉取registry镜像

```
[dong@localhost ~]$ docker pull registry:2.6.2
2.6.2: Pulling from library/registry
4064ffdc82fe: Pull complete
c12c92d1c5a2: Pull complete
4fbc9b6835cc: Pull complete
765973b0f65f: Pull complete
3968771a7c3a: Pull complete
Digest: sha256:51bb55f23ef7e25ac9b8313b139a8dd45baa832943c8ad8f7da2ddad6355b3c8
Status: Downloaded newer image for registry:2.6.2
```

创建本地镜像数据目录，并映射到registry的容器中启动

```
[dong@localhost ~]$ sudo mkdir -p /opt/data/registry
[dong@localhost ~]$ sudo chown root:docker /opt/data/registry
[dong@localhost ~]$ sudo chmod 775 /opt/data/registry
[dong@localhost ~]$ ls -l /opt/data
total 0
drwxrwxr-x. 2 root docker 6 Aug 13 11:25 registry

[dong@localhost ~]$ docker run -d -p 5000:5000 -v /opt/data/registry:/var/lib/registry/docker/registry registry:2.6.2
9a41d0b4793cc46e67252e463f6acf056853f206b2a7bc09f63a3d748723ee02
```

上传镜像到私有仓库

```
[dong@localhost ~]$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
9a41d0b4793c        registry:2.6.2      "/entrypoint.sh /etc…"   13 seconds ago      Up 12 seconds       0.0.0.0:5000->5000/tcp   amazing_meitner

[dong@localhost ~]$ docker tag alpine:notes.imported.3.8 172.16.180.133:5000/alpine:notes.imported.3.8

[dong@localhost ~]$ docker push 172.16.180.133:5000/alpine:notes.imported.3.8
The push refers to repository [172.16.180.133:5000/alpine]
b84976490cfc: Pushed
notes.imported.3.8: digest: sha256:5094a63425ebdd279e8303893014d7f98689d04e63740194050efa3fb8cddedf size: 527
```

获取仓库服务器中的镜像列表，并查看本地的仓库数据目录

```
[dong@localhost ~]$ curl http://172.16.180.133:5000/v2/_catalog
{"repositories":["alpine"]}

[dong@localhost ~]$ ls /opt/data/registry/v2/repositories/
alpine
```
