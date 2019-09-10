# Docker Notes
![Docker logo](https://www.docker.com/sites/default/files/mono-vertical.png)

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

## 20190910
### Docker实战与进阶

Docker新版本已经不基于LXC

Docker镜像没有内核，内核特性受限于宿主机

镜像导入导出

```
[centos@sandbox-docker-1 ~]$ docker save -o centos.tar centos:latest
```

容器输出并退出

```
[centos@sandbox-docker-1 ~]$ docker run centos /bin/echo "Hello world”

[centos@sandbox-docker-1 ~]$ docker ps -a
```

Docker 默认在前台执行任务，执行完成就结束

默认启动的初始进程/bin/bash的PID为1

当PID为1的进程退出时，容器的生命周期结束

```
[centos@sandbox-docker-1 ~]$ docker run --name mydocker -t -i centos /bin/bash
[root@798331b40019 /]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.4  0.0  11820  1888 pts/0    Ss   02:09   0:00 /bin/bash
root        14  0.0  0.0  51740  1740 pts/0    R+   02:09   0:00 ps aux
```

参看所有容器

```
[centos@sandbox-docker-1 ~]$ docker ps -a
```

启动容器

```
[centos@sandbox-docker-1 ~]$ docker start mydocker
```

进入容器

```
[centos@sandbox-docker-1 ~]$ docker exec
```

在util-linux软件包当中(namespace enter)

```
[centos@sandbox-docker-1 ~]$ nsenter
```

获取容器详情

```
[centos@sandbox-docker-1 ~]$ docker inspect mydocker
```

获取容器第一个进程的PID

```
[centos@sandbox-docker-1 ~]$ docker inspect --format "{{.State.Pid}}” mydocker
14424
```

```
[centos@sandbox-docker-1 ~]$ sudo nsenter --target 14424 --mount --uts --ipc --net --pid
```

进入镜像

```
[centos@sandbox-docker-1 ~]$ docker exec -it mydocker /bin/bash
```

直接执行一个命令

```
[centos@sandbox-docker-1 ~]$ docker exec mydocker whoami
root
```

docker attach 会直接进入PID为1的/bin/bash，所以exit之后会导致容器终止

运行并自动删除容器

```
[centos@sandbox-docker-1 ~]$ docker run --rm centos /bin/echo "Run once and quit"
```

运行Nginx容器

```
[centos@sandbox-docker-1 ~]$ docker pull nginx
```

-P为指定随机端口

```
[centos@sandbox-docker-1 ~]$ docker run -d -P nginx
d1cdc6eff3f2f740db3c6dcb090359922d1dce5db14d1fd5fd244c59b4b84722

[centos@sandbox-docker-1 ~]$ docker ps -l
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                   NAMES
d1cdc6eff3f2        nginx               "nginx -g 'daemon of…"   6 seconds ago       Up 5 seconds        0.0.0.0:32768->80/tcp   practical_archimedes

curl http://localhost:32768

[centos@sandbox-docker-1 ~]$ docker logs d1cdc6eff3f2
172.17.0.1 - - [10/Sep/2019:03:16:26 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.29.0" “-"

[centos@sandbox-docker-1 ~]$ docker run -d -p 3333:80 --name nginx-demo nginx
e1a01aea8a5a51841a8ff721d47f2b09d1f9b31b7946d6dcca3df1aebebfeecc

[centos@sandbox-docker-1 ~]$ curl http://localhost:3333
```


指定IP与端口映射

```
-p hostPort:containerPort
-p ip:hostPort:containerPort
-p ip::containerPort
-p hostPort:containerPort:udp
```

查看映射端口

```
[centos@sandbox-docker-1 ~]$ docker port nginx-demo
80/tcp -> 0.0.0.0:3333
```

Docker数据卷

```
-v /data
-v src:dst
```

数据卷容器

```
--volumes-from
```

数据卷默认路径

```
[centos@sandbox-docker-1 dong]$ docker run -d --rm --name nginx-test1 -v data nginx
058adf993d3ecd70de54b2236e4b8dce50c006965dc3a548754a5e8441fb114d

[centos@sandbox-docker-1 dong]$ docker exec -it nginx-test1 /bin/bash
root@058adf993d3e:/# touch /data/test.txt
root@058adf993d3e:/# exit
exit

[centos@sandbox-docker-1 dong]$ docker inspect nginx-test1 | grep Source | grep data
"Source": "/var/lib/docker/volumes/6e61fbd064d570c989e2894a8366c950797969a310da5b6f1694b5be1d8a11fb/_data",

[centos@sandbox-docker-1 dong]$ sudo ls /var/lib/docker/volumes/6e61fbd064d570c989e2894a8366c950797969a310da5b6f1694b5be1d8a11fb/_data
test.txt
```

数据卷指定挂载路径

```
mkdir -p /home/centos/data/docker-volume-nginx
docker run -d --name nginx-test2 -v /home/centos/data/docker-volume-nginx:/data nginx
38ebc34f13382ace77e877f35d70eb3b10bb6e7af73fc7a3f90f8bc10492bb16

[centos@sandbox-docker-1 ~]$ docker exec -it nginx-test2 /bin/bash
root@38ebc34f1338:/# ls /data/
root@38ebc34f1338:/# touch /data/seeme.txt
root@38ebc34f1338:/# exit
exit

[centos@sandbox-docker-1 ~]$ ls /home/centos/data/docker-volume-nginx/
seeme.txt
```

通用环境给开发人员的开发环境:
Windows - VMware共享目录 - Docker数据卷容器映射到该共享目录 - 其它的容器启动时引用该数据卷容器

数据卷容器是一种特殊的容器,用于简化配置参数,使其它容器可直接通过--volumes-from引用,并且volume-demo容器即使挂掉也不会影响引用过它的容器

```
[centos@sandbox-docker-1 ~]$  docker run -d --name volume-demo -v /data:/data nginx
90cc697f7955fa24d92a446fa80292e622a2f0bd17b949410352ef8e4b637fde

[centos@sandbox-docker-1 ~]$ docker ps | grep volume-demo
90cc697f7955        nginx               "nginx -g 'daemon of…"   12 seconds ago      Up 11 seconds       80/tcp                  volume-demo

[centos@sandbox-docker-1 ~]$ sudo touch /data/hello.txt

[centos@sandbox-docker-1 ~]$ docker run -d --name nginx-volume-demo --volumes-from volume-demo nginx
f9e9e6393aff4b903168d7bb92451d32cdcfb412a068a1e5ed97085082a2861c

[centos@sandbox-docker-1 ~]$ docker exec -it nginx-volume-demo /bin/bash
root@f9e9e6393aff:/# ls /data/
hello.txt
root@f9e9e6393aff:/# exit
exit

[centos@sandbox-docker-1 ~]$ docker ps | grep volume-demo
f9e9e6393aff        nginx               "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes        80/tcp                  nginx-volume-demo
90cc697f7955        nginx               "nginx -g 'daemon of…"   5 minutes ago       Up 5 minutes        80/tcp                  volume-demo
[centos@sandbox-docker-1 ~]$ docker stop volume-demo
volume-demo
[centos@sandbox-docker-1 ~]$ docker ps | grep volume-demo
f9e9e6393aff        nginx               "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes        80/tcp                  nginx-volume-demo

[centos@sandbox-docker-1 ~]$ docker exec -it nginx-volume-demo /bin/bash
root@f9e9e6393aff:/# ls /data/
hello.txt
root@f9e9e6393aff:/# exit
exit
```

手动构建镜像,手工操作测试并梳理所有的步骤,是编写Dockerfile的前提

```
[centos@sandbox-docker-1 ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              67fa590cfc1c        2 weeks ago         202MB
nginx               latest              5a3221f0137b        3 weeks ago         126MB

[centos@sandbox-docker-1 ~]$ docker run --name mynginx -it centos /bin/bash
[root@697f787c6fde /]# yum install -y epel-release
[root@697f787c6fde /]# yum install -y nginx
[root@697f787c6fde /]# yum clean all
```

设置 daemon off; 使Nginx在前台运行避免容器自动终止

```
[root@697f787c6fde /]# vi /etc/nginx/nginx.conf
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/


daemon off;
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
...

[root@697f787c6fde /]# exit
exit

[centos@sandbox-docker-1 ~]$ docker ps -a | grep mynginx
697f787c6fde        centos              "/bin/bash"              7 minutes ago       Exited (0) About a minute ago                           mynginx

[centos@sandbox-docker-1 ~]$ docker commit -m "my nginx" 697f787c6fde mynginx:v1
sha256:0a60f3a2ec3d7e67b8ebd817e666fb397d37195a67d68824f8cf89c2659ebae4

[centos@sandbox-docker-1 ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mynginx             v1                  0a60f3a2ec3d        10 seconds ago      281MB
centos              latest              67fa590cfc1c        2 weeks ago         202MB

[centos@sandbox-docker-1 ~]$ docker run -d -p 6080:80 mynginx:v1 nginx
5020e251dfcf5c7c147232b3275c9b6b6ef0594a5d1b96979bd2af0cdb0b7c69

[centos@sandbox-docker-1 ~]$ docker ps | grep mynginx:v1
5020e251dfcf        mynginx:v1          "nginx"                  34 seconds ago      Up 34 seconds       0.0.0.0:6080->80/tcp    romantic_ellis

[centos@sandbox-docker-1 ~]$ curl http://localhost:6080
```

实用Dockerfile自动构建

https://docs.docker.com/engine/reference/builder/

```bash
FROM #基础镜像信息
MAINTAINER #维护者信息

#镜像操作指令
RUN
ADD
WORKDIR
VOLUME
EXPOSE

CMD #容器启动时执行指令
```

```
[centos@sandbox-docker-1 ~]$ sudo mkdir -p /opt/dockerfile/mynginx
[centos@sandbox-docker-1 ~]$ sudo chown -R centos:centos /opt/dockerfile
[centos@sandbox-docker-1 ~]$ cd /opt/dockerfile/mynginx/

[centos@sandbox-docker-1 mynginx]$ cat Dockerfile
FROM centos

MAINTAINER mcsrainbow guosuiyu@gmail.com

RUN yum install epel-release -y \
    && yum install -y nginx \
    && echo "daemon off;" >> /etc/nginx/nginx.conf

ADD index.html /usr/share/nginx/html/index.html

EXPOSE 80

CMD ["nginx”]

[centos@sandbox-docker-1 mynginx]$ echo "nginx in docker" > index.html
[centos@sandbox-docker-1 mynginx]$ ll
total 8
-rw-rw-r-- 1 centos centos 274 Sep 10 05:10 Dockerfile
-rw-rw-r-- 1 centos centos  16 Sep 10 05:11 index.html
[centos@sandbox-docker-1 mynginx]$ docker build -t mynginx:v2 .
...
Successfully built 788b272124bc
Successfully tagged mynginx:v2

[centos@sandbox-docker-1 mynginx]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mynginx             v2                  788b272124bc        26 seconds ago      408MB
mynginx             v1                  0a60f3a2ec3d        18 minutes ago      281MB
centos              latest              67fa590cfc1c        2 weeks ago         202MB
```

Docker生产镜像构建实战

安装 - 配置 - 启动 三步入门

```
安装:
包管理工具
二进制
源码编译

配置类型:
网络,路径,安全,性能,容量,功能,日志

启动:
系统服务
直接执行
```

Docker镜像是分层存储的

分层构建设计

```
1. 设计一个公司的操作系统镜像
2. 操作系统镜像之上的运行环境
3. 运行环境之上的应用
```
 
```
[centos@sandbox-docker-1 mynginx]$ cd /opt/dockerfile/
[centos@sandbox-docker-1 dockerfile]$ mkdir system runtime app

[centos@sandbox-docker-1 dockerfile]$ cd system/
[centos@sandbox-docker-1 system]$ mkdir centos
[centos@sandbox-docker-1 system]$ cd centos/
[centos@sandbox-docker-1 centos]$ cat Dockerfile
FROM centos

MAINTAINER mcsrainbow guosuiyu@gmail.com

RUN yum install -y epel-release \
    && yum install -y wget sudo vim git tree net-tools \
    && yum clean all

[centos@sandbox-docker-1 centos]$ docker build -t system/centos:v1 .
...
Successfully built 8ca8475ceced
Successfully tagged system/centos:v1


[centos@sandbox-docker-1 centos]$ cd ..
[centos@sandbox-docker-1 system]$ ll
total 0
drwxrwxr-x 2 centos centos 24 Sep 10 05:27 centos
[centos@sandbox-docker-1 system]$ mkdir centos-ssh
[centos@sandbox-docker-1 system]$ cd centos-ssh
[centos@sandbox-docker-1 centos-ssh]$ cat Dockerfile
FROM centos

MAINTAINER mcsrainbow guosuiyu@gmail.com

RUN yum install -y epel-release \
    && yum install -y wget sudo vim git tree net-tools \
    && yum install -y openssh-clients openssh-server openssh-devel \
    && yum clean all \
    && ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key \
    && ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key \
    && ssh-keygen -A -t dsa -f /etc/ssh/ssh_host_dsa_key \
    && echo 'root:heydevops' | chpasswd

[centos@sandbox-docker-1 centos-ssh]$ docker build -t system/centos:openssh_v1 .
...
Successfully built 8df0b0fc9324
Successfully tagged system/centos:openssh_v1

[centos@sandbox-docker-1 centos-ssh]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
system/centos       openssh_v1          8df0b0fc9324        About a minute ago   300MB
system/centos       v1                  8ca8475ceced        11 minutes ago       299MB
mynginx             v2                  788b272124bc        26 minutes ago       408MB
mynginx             v1                  0a60f3a2ec3d        44 minutes ago       281MB
centos              latest              67fa590cfc1c        2 weeks ago          202MB

[centos@sandbox-docker-1 centos-ssh]$ docker run -d --name centos-ssh-demo -p 8022:22 system/centos:openssh_v1 /usr/sbin/sshd -D
95d7b4f357d4ebf34f7964a86af79521f11a99e1157115191b0f17dd64261fd1

[centos@sandbox-docker-1 centos-ssh]$ docker ps | grep centos-ssh-demo
95d7b4f357d4        system/centos:openssh_v1   "/usr/sbin/sshd -D"      20 seconds ago      Up 19 seconds       0.0.0.0:8022->22/tcp    centos-ssh-demo

[centos@sandbox-docker-1 centos-ssh]$ ssh -p 8022 root@localhost
root@localhost's password: heydevops
```

可以看到sshd的PID为1,因此该进程结束会导致容器终止,且Docker只允许在前台运行一个进程

```
[root@95d7b4f357d4 ~]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0 112864  4308 ?        Ss   05:43   0:00 /usr/sbin/sshd -D
```

使用Supervisor进程管理工具,使其作为PID为1的进程来管理其它的进程

```
[root@95d7b4f357d4 ~]# yum install -y supervisor
[root@95d7b4f357d4 ~]# ls -l /etc/supervisord.*
-rw-r--r-- 1 root root 7953 Jul 28  2017 /etc/supervisord.conf

/etc/supervisord.d:
total 0

[root@95d7b4f357d4 ~]# sed -i s/nodaemon=false/nodaemon=true/g /etc/supervisord.conf
[root@95d7b4f357d4 ~]# grep daemon /etc/supervisord.conf
nodaemon=true               ; (start in foreground if true;default false)

[root@95d7b4f357d4 ~]# tail -n2 /etc/supervisord.conf
[include]
files = supervisord.d/*.ini

[root@95d7b4f357d4 ~]# exit
exit
```

创建supervisord.conf与sshd.ini

```
[centos@sandbox-docker-1 centos-ssh]$ /opt/dockerfile/system/centos-ssh
```

```ini
[centos@sandbox-docker-1 centos-ssh]$ cat supervisord.conf
[unix_http_server]
file=/var/run/supervisor/supervisor.sock   ; (the path to the socket file)

[supervisord]
logfile=/var/log/supervisor/supervisord.log  ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB       ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10          ; (num of main logfile rotation backups;default 10)
loglevel=info               ; (log level;default info; others: debug,warn,trace)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
nodaemon=true               ; (start in foreground if true;default false)
minfds=1024                 ; (min. avail startup file descriptors;default 1024)
minprocs=200                ; (min. avail process descriptors;default 200)
umask=022                   ; process file creation umask; default 022
user=root                   ; setuid to this UNIX account at startup; recommended if root

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor/supervisor.sock ; use a unix:// URL  for a unix socket

[include]
files = supervisord.d/*.ini

[centos@sandbox-docker-1 centos-ssh]$ cat sshd.ini
[program:sshd]
command=/usr/sbin/sshd -D     ; the program (relative uses PATH, can take args)
process_name=%(program_name)s ; process_name expr (default %(program_name)s)
autostart=true                ; start at supervisord start (default: true)
```

```
[centos@sandbox-docker-1 centos-ssh]$ cat Dockerfile
FROM centos

MAINTAINER mcsrainbow guosuiyu@gmail.com

RUN yum install -y epel-release \
    && yum install -y wget sudo vim git tree net-tools \
    && yum install -y openssh-clients openssh-server openssh-devel \
    && yum install -y supervisor \
    && yum clean all \
    && ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key \
    && ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key \
    && ssh-keygen -A -t dsa -f /etc/ssh/ssh_host_dsa_key \
    && echo 'root:heydevops' | chpasswd

ADD supervisord.conf /etc/supervisord.conf
ADD sshd.ini /etc/supervisord.d/sshd.ini

EXPOSE 22

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf”]

[centos@sandbox-docker-1 centos-ssh]$ docker build -t system/centos:supervisord_v1 .
...
Successfully built 11c8048b252c
Successfully tagged system/centos:supervisord_v1

[centos@sandbox-docker-1 centos-ssh]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
system/centos       supervisord_v1      11c8048b252c        24 seconds ago      304MB
centos              latest              67fa590cfc1c        2 weeks ago         202MB
dremio/dremio-oss   latest              09034556f58c        3 weeks ago         865MB

[centos@sandbox-docker-1 centos-ssh]$ docker run -d --name centos-ssh-supervisor -p 2222:22 system/centos:supervisord_v1
64a422ae342162d0b16b2b56d745df8d9003f7ba233816900c793eca98db693e

[centos@sandbox-docker-1 centos-ssh]$ ssh -p 2222 root@localhost
root@localhost's password: heydevops
[root@64a422ae3421 ~]#

[root@64a422ae3421 ~]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.2  0.1 117804 14868 ?        Ss   09:15   0:00 /usr/bin/python /usr/bin/supervisord -c /etc/supervisord.conf
root         8  0.0  0.0 112864  4308 ?        S    09:15   0:00 /usr/sbin/sshd -D
```

构建Supervisor+Tomcat

```
[centos@sandbox-docker-1 centos-ssh]$ cd /opt/dockerfile/
[centos@sandbox-docker-1 dockerfile]$ ll
total 0
drwxrwxr-x 2 centos centos  6 Sep 10 05:22 app
drwxr-xr-x 2 centos centos 42 Sep 10 05:11 mynginx
drwxrwxr-x 2 centos centos  6 Sep 10 05:22 runtime
drwxrwxr-x 4 centos centos 38 Sep 10 05:30 system

[centos@sandbox-docker-1 dockerfile]$ mkdir runtime/tomcat
[centos@sandbox-docker-1 dockerfile]$ cd runtime/tomcat/
[centos@sandbox-docker-1 tomcat]$ ls -l
total 9504
-rw-rw-r-- 1 centos centos 9722154 Aug 14 22:46 apache-tomcat-8.5.45.tar.gz
-rw-rw-r-- 1 centos centos     510 Sep 10 10:03 Dockerfile
-rw-rw-r-- 1 centos centos     108 Sep 10 09:56 tomcat.ini
[centos@sandbox-docker-1 tomcat]$ cat Dockerfile
FROM system/centos:supervisord_v1

MAINTAINER mcsrainbow guosuiyu@gmail.com

RUN yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel \
    && yum clean all

ENV JAVA_HOME /usr/lib/jvm/java-1.8.0-openjdk

# Auto extract the compressed packages
ADD apache-tomcat-8.5.45.tar.gz /usr/local
RUN ln -s /usr/local/apache-tomcat-8.5.45 /usr/local/tomcat

ADD tomcat.ini /etc/supervisord.d/tomcat.ini
ENV TOMCAT_HOME /usr/local/tomcat

EXPOSE 22 8080

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]

[centos@sandbox-docker-1 tomcat]$ cat tomcat.ini
[program:tomcat]
command=/usr/local/tomcat/bin/catalina.sh run
process_name=%(program_name)s
autostart=true

[centos@sandbox-docker-1 tomcat]$ docker build -t runtime/tomcat:v1 .
...
Successfully built e66e80cb2db5
Successfully tagged runtime/tomcat:v1

[centos@sandbox-docker-1 tomcat]$ docker run -d --name tomcat-demo -p 6080:8080 -p 6022:22 runtime/tomcat:v1
0f23f4e1b085f53daee9c387b44de520144c9aae5d88518d547a1d7b5fc63859

[centos@sandbox-docker-1 tomcat]$ docker ps -l
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                          NAMES
0f23f4e1b085        runtime/tomcat:v1   "/usr/bin/supervisor…"   11 seconds ago      Up 10 seconds       0.0.0.0:6022->22/tcp, 0.0.0.0:6080->8080/tcp   tomcat-demo

[centos@sandbox-docker-1 tomcat]$ curl http://localhost:6080
[centos@sandbox-docker-1 tomcat]$ ssh -p 6022 root@localhost
root@localhost's password: heydevops
[root@0f23f4e1b085 ~]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.1  0.1 117808 14876 ?        Ss   10:11   0:00 /usr/bin/python /usr/bin/supervisord -c /etc/supervisord.conf
root         9  0.0  0.0 112864  4308 ?        S    10:11   0:00 /usr/sbin/sshd -D
root        10  6.4  1.9 4538264 153628 ?      Sl   10:11   0:04 /usr/lib/jvm/java-1.8.0-openjdk/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties

[root@0f23f4e1b085 ~]# exit
logout
Connection to localhost closed.
```

构建运行在Tomcat中的Jenkins应用镜像

```
[centos@sandbox-docker-1 tomcat]$ cd ../..
[centos@sandbox-docker-1 dockerfile]$ ls
app  mynginx  runtime  system
[centos@sandbox-docker-1 dockerfile]$ cd app/
[centos@sandbox-docker-1 app]$ mkdir jenkins
[centos@sandbox-docker-1 app]$ cd jenkins/

[centos@sandbox-docker-1 jenkins]$ ll
total 75556
-rw-rw-r-- 1 centos centos      186 Sep 10 10:21 Dockerfile
-rw-rw-r-- 1 centos centos 77364728 Aug 26 16:07 jenkins.war
[centos@sandbox-docker-1 jenkins]$ cat Dockerfile
FROM runtime/tomcat:v1

MAINTAINER mcsrainbow guosuiyu@gmail.com

ADD jenkins.war /usr/local/tomcat/webapps/

EXPOSE 22 8080

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf”]

[centos@sandbox-docker-1 jenkins]$ docker build -t app/jenkins:v1 .
Successfully built a4929d2bf19e
Successfully tagged app/jenkins:v1

[centos@sandbox-docker-1 jenkins]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
app/jenkins         v1                  a4929d2bf19e        31 seconds ago      649MB
runtime/tomcat      v1                  e66e80cb2db5        18 minutes ago      572MB
system/centos       supervisord_v1      11c8048b252c        About an hour ago   304MB
centos              latest              67fa590cfc1c        2 weeks ago         202MB

[centos@sandbox-docker-1 jenkins]$ docker run -d --name jenkins-demo -p 5022:22 -p 5080:8080 app/jenkins:v1
f45ae9580334dc298484b8dd44665bcf4fcef8771022b7611ffaf8451616cc96

[centos@sandbox-docker-1 tomcat]$ curl http://localhost:5080
[centos@sandbox-docker-1 tomcat]$ ssh -p 5022 root@localhost
root@localhost's password: heydevops
[root@f45ae9580334 ~]# exit
logout
Connection to localhost closed.
```

Docker镜像仓库

```
[centos@sandbox-docker-1 ~]$ docker run -d --name docker-registry -p 5000:5000 registry
2450955f457f2724d68e925b74d7656035a077ea25ca0d6056672488c23ac5e3

[centos@sandbox-docker-1 ~]$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                                                        NAMES
2450955f457f        registry            "/entrypoint.sh /etc…"   22 seconds ago      Up 21 seconds       0.0.0.0:5000->5000/tcp                                                       docker-registry

[centos@sandbox-docker-1 ~]$ vim /etc/docker/daemon.json
{
  "insecure-registries":[
    "172.31.3.154:5000"
  ]
}
[centos@sandbox-docker-1 ~]$ sudo systemctl restart docker
[centos@sandbox-docker-1 ~]$ sudo systemctl status docker

[centos@sandbox-docker-1 ~]$ docker start docker-registry
docker-registry

[centos@sandbox-docker-1 ~]$ docker tag runtime/tomcat:v1 172.31.3.154:5000/runtime/tomcat:v1
[centos@sandbox-docker-1 ~]$ docker images | grep tomcat
172.31.3.154:5000/runtime/tomcat   v1                  e66e80cb2db5        2 hours ago         572MB
runtime/tomcat                     v1                  e66e80cb2db5        2 hours ago         572MB

[centos@sandbox-docker-1 ~]$ docker push 172.31.3.154:5000/runtime/tomcat:v1

[centos@sandbox-docker-1 ~]$ docker rmi runtime/tomcat:v1 172.31.3.154:5000/runtime/tomcat:v1
Untagged: runtime/tomcat:v1
Untagged: 172.31.3.154:5000/runtime/tomcat:v1
Untagged: 172.31.3.154:5000/runtime/tomcat@sha256:eef76558d355cac36d327d8a5709c059df21434b103193c4f7d9d9e6a7cf9609

[centos@sandbox-docker-1 ~]$ docker pull 172.31.3.154:5000/runtime/tomcat:v1
...
Status: Downloaded newer image for 172.31.3.154:5000/runtime/tomcat:v1
172.31.3.154:5000/runtime/tomcat:v1
```

使用harbor搭建仓库

https://goharbor.io

```
[centos@sandbox-docker-1 ~]$ mkdir harbor
[centos@sandbox-docker-1 ~]$ cd harbor
[centos@sandbox-docker-1 harbor]$ wget https://storage.googleapis.com/harbor-releases/release-1.9.0/harbor-offline-installer-v1.9.0-rc1.tgz
[centos@sandbox-docker-1 harbor]$ ls
harbor-offline-installer-v1.9.0-rc1.tgz
[centos@sandbox-docker-1 harbor]$ tar xzf harbor-offline-installer-v1.9.0-rc1.tgz
[centos@sandbox-docker-1 harbor]$ sudo yum install -y docker-compose
[centos@sandbox-docker-1 harbor]$ cd harbor
[centos@sandbox-docker-1 harbor]$ ls -l
total 605144
-rw-r--r-- 1 centos centos 619632806 Sep  4 01:43 harbor.v1.9.0.tar.gz
-rw-r--r-- 1 centos centos      5805 Sep  4 01:43 harbor.yml
-rwxr-xr-x 1 centos centos      5088 Sep  4 01:43 install.sh
-rw-r--r-- 1 centos centos     11347 Sep  4 01:43 LICENSE
-rwxr-xr-x 1 centos centos      1748 Sep  4 01:43 prepare

[centos@sandbox-docker-1 ~]$ vim /etc/docker/daemon.json
{
  "insecure-registries":[
    "172.31.3.154:5000",
    "172.31.3.154:80"
  ]
}
[centos@sandbox-docker-1 ~]$ sudo systemctl restart docker

[centos@sandbox-docker-1 harbor]$ vim harbor.yml
...
hostname: 172.31.3.154
...
# Remember Change the admin password from UI after launching Harbor.
harbor_admin_password: heydevops


# Harbor DB configuration
database:
  # The password for the root user of Harbor DB. Change this before any production use.
  password: heydevops
...

[centos@sandbox-docker-1 harbor]$ sudo ./install.sh
...
✔ ----Harbor has been installed and started successfully.----

Now you should be able to visit the admin portal at http://172.31.3.154.
For more details, please visit https://github.com/goharbor/harbor .

[centos@sandbox-docker-1 harbor]$ sudo docker-compose ps
      Name                     Command               State             Ports
--------------------------------------------------------------------------------------
harbor-core         /harbor/harbor_core              Up
harbor-db           /docker-entrypoint.sh            Up      5432/tcp
harbor-jobservice   /harbor/harbor_jobservice  ...   Up
harbor-log          /bin/sh -c /usr/local/bin/ ...   Up      127.0.0.1:1514->10514/tcp
harbor-portal       nginx -g daemon off;             Up      8080/tcp
nginx               nginx -g daemon off;             Up      0.0.0.0:80->8080/tcp
redis               redis-server /etc/redis.conf     Up      6379/tcp
registry            /entrypoint.sh /etc/regist ...   Up      5000/tcp
registryctl         /harbor/start.sh                 Up
```

登陆并上传镜像到harbor仓库

```
[centos@sandbox-docker-1 harbor]$ docker login 172.31.3.154:80
Username: admin
Password: heydevops
Login Succeeded

[centos@sandbox-docker-1 harbor]$ docker tag app/jenkins:v1 172.31.3.154:80/app/jenkins:v1
[centos@sandbox-docker-1 harbor]$ docker push 172.31.3.154:80/app/jenkins:v1
v1: digest: sha256:ac89ee9ce22ea9d401a60cf02d39c84100e477e88106e326979652cd3fc54ed4 size: 2204
```
