# Docker Notes

**参考资料** [Docker容器实战](http://www.devopsedu.com/front/couinfo/13)

## Docker基础实战

Docker新版本已经不基于LXC

Docker镜像没有内核，内核特性受限于宿主机

导出镜像

```
[centos@sandbox-docker-1 ~]$ docker save -o centos.tar centos:latest
```

运行容器并退出

```
[centos@sandbox-docker-1 ~]$ docker run centos /bin/echo "Hello world"

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

进入容器

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

运行容器

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
[centos@sandbox-docker-1 ~]$ docker run -d --name volume-demo -v /data:/data nginx
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

## 使用Dockerfile自动构建

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

## Docker生产镜像构建实战

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

### 分层构建设计

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

### 使用Supervisor进程管理工具,使其作为PID为1的进程来管理其它的进程

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

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]

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

### 构建Supervisor+Tomcat

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

### 构建运行在Tomcat中的Jenkins应用镜像

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

## Docker镜像仓库

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

## 使用harbor搭建仓库

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
