#### Index
<!--ts-->
[ansible](https://github.com/mcsrainbow/notes/tree/master/ansbile)
[aptitude](#aptitude)
[at](#at)
[awk](#awk)
[aws](#aws)
[curl](#curl)
[conda](#conda)
[cron](#cron)
[docker](#docker)
[du](#du)
[fallocate](#fallocate)
[find](#find)
[firewalld](#firewalld)
[fpm](#fpm)
[git](https://github.com/mcsrainbow/notes/blob/master/git/git_notes.md)
[helm](#helm)
[iptables](#iptables)
[jq](#jq)
[kubectl](#kubectl)
[lftp](#lftp)
[lvm](#lvm)
[mysql](https://github.com/mcsrainbow/notes/blob/master/db/mysql.md)
[nc](#nc)
[openssl](https://github.com/mcsrainbow/notes/tree/master/certs)
[pip](#pip)
[postgresql](#postgresql)
[python](#python)
[route](#route)
[rpm](#rpm)
[rsync](#rsync)
[sed](#sed)
[setfacl](#setfacl)
[ssh](#ssh)
[systemd](#systemd)
[tar](#tar)
[tcpdump](#tcpdump)
[uv](#uv)
[wget](#wget)
[yq](#yq)
[yum](#yum)
[zip](#zip)
[misc](#misc)
<!--te-->

#### aptitude
```bash
aptitude update
aptitude upgrade
aptitude install krb5-multidev

# /etc/apt/sources.list
# http://archive.ubuntu.com/ubuntu/dists/
# http://old-releases.ubuntu.com/ubuntu/dists/
```

#### at
```bash
echo /opt/bin/run.py | at 5pm + 3 days

atq
at -c 1
atrm 1
```

#### awk
```bash
awk -F: '($3<1000){print $1}' /etc/passwd

aws ecr describe-repositories --output text | awk '($1~/^REPO/){print $NF}'
yarn application -list | awk '(NR>2){print $1}'

ss -4lntp | awk 'NR==1 {print $1"\t"$5} NR>1 {print $1"\t"$4"\t"$6}'
```

#### aws
```bash
$(aws ecr get-login --no-include-email --region ap-east-1 --registry-ids 857857857857)
$(aws ecr get-login --no-include-email --region ap-east-1)

docker_ecr_password=$(aws ecr get-login-password --region ap-east-1)
docker login --username AWS --password $docker_ecr_password 857857857857.dkr.ecr.ap-east-1.amazonaws.com

aws s3 ls --summarize --human-readable --recursive s3://bucket-name

# if not run as aws account b
aws s3 sync s3://account-a-bucket/folder/ s3://account-b-bucket/folder/ --acl bucket-owner-full-control

# if s3 vpc endpoints disabled
aws s3 sync s3://src-bucket/folder/ s3://dst-bucket/folder/ --source-region us-west-1 --region us-west-2

aws sts get-caller-identity
```

#### curl
```bash
curl -sL https://www.opscode.com/chef/install.sh | sudo bash

curl --fail -u username:password --upload-file heylinux-app.rpm http://nexus.heylinux.com/repository/devops/rpms/

curl --progress-bar -o /tmp/heylinux-app.rpm http://nexus.heylinux.com/repository/devops/rpms/heylinux-app.rpm

curl -XPUT -k -u 'username:password' 'https://service.heylinux.com/api/subject/' \
-H 'Content-Type: application/json' \
-d '{"key":"value"}'

curl -s -w "connect time: %{time_connect}s, time to first byte: %{time_starttransfer}s, \
total time: %{time_total}s, num connects: %{num_connects}, speed download: %{speed_download}bytes/s, \
speed upload: %{speed_upload}bytes/s, time prexfer: %{time_pretransfer}s \n" \
-o /dev/null https://blog.heylinux.com

curl --header "PRIVATE-TOKEN: <your_access_token>" \
"https://jihulab.com/api/v4/projects/857857/repository/files/folder%2Fpackage.tar/raw?ref=main" \
-o package.tar
```

#### conda
```bash
bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/miniconda

source /opt/miniconda/etc/profile.d/conda.sh

conda config --append channels conda-forge
conda create -n heylinux python=3.8

conda activate heylinux

conda search boto3
conda search | grep r-aws

conda install paramiko boto3
conda list | grep boto3

echo 'use_only_tar_bz2: true' >> ~/.condarc
conda install --download-only -c conda-forge s3fs==0.3.5

source /opt/miniconda/bin/activate heylinux

#!/opt/miniconda/envs/heylinux/bin/python

conda list -e > requirements.txt
conda create --name heylinux --file requirements.txt
```

#### cron
```bash
echo damonguo >> /etc/cron.allow

crontab -u damonguo -l
---
## minute hour day-of-month month day-of-week(0/7=sun)
##  0-59  0-23     1-31     1-12  0-7/mon,tue,wed,thu,fri,sat,sun
# OPS-857: run /opt/bin/run.py every 5 minutes and log all outputs and alert if fail
MAILTO="ops-alerts@heylinux.com"
*/5 * * * * ps aux | grep /opt/bin/run.py | grep -v grep || /opt/bin/run.py >> /opt/logs/run.out 2> >(tee -a /opt/logs/run.err >&2)
```

#### [docker](https://github.com/mcsrainbow/notes/tree/master/docker)
```bash
# override ENTRYPOINT and CMD in built image
    stdin_open: true
    tty: true
    entrypoint: ["/bin/sh","-c"]
    command: ["sleep infinity"]
```

#### du
```bash
du -shc /path/to/dir/*
du -ah --max-depth 1 --exclude='proc' | sort -k2 | awk '($1 ~ /M|G|T/){print $1"\t"$2}'
du -ah --max-depth 1 --exclude='proc' | awk '($1 ~ /G/){print $1"\t"$2}' | sort -rn
du -am --max-depth 1 | sort -rn | awk '($1 > 100){print $1"M\t"$2}'
du -am --max-depth 1 | sort -rn | awk '($1 > 100){print $1" "$2}' | sed ':a;s/\B[0-9]\{3\}\>/,&/;ta' | awk '{print $1"M\t"$2}'
```

#### fallocate
```bash
fallocate -l 200m 200m.size
fallocate -l 10g 10g.size
```

#### find
```bash
find . -maxdepth 1 -type f -size +100M -exec /bin/ls -lh {} + | sort -k2 | awk '{print $5"\t"$NF}'
find -type f -size +100M -exec ls -lh {} + | awk '{print $5"\t"$NF}' | sort -rn

sudo find /opt/app -name log4j-core-*.jar -exec zip -q -d {} org/apache/logging/log4j/core/lookup/JndiLookup.class \;
sudo find /opt/app -name log4j-core-*.jar | xargs -I {} sudo zip -q -d {} org/apache/logging/log4j/core/lookup/JndiLookup.class
```

#### firewalld
```bash
firewall-cmd --state
firewall-cmd --get-default-zone
firewall-cmd --get-zones

firewall-cmd --zone=internal --change-interface=eth0
firewall-cmd --zone=internal --remove-interface=eth0
firewall-cmd --zone=public --add-interface=eth0
firewall-cmd --get-active-zones

firewall-cmd --list-all
firewall-cmd --zone=public --list-all

firewall-cmd --zone=public --permanent --add-service=https
firewall-cmd --zone=public --permanent --add-port=1857/tcp
firewall-cmd --zone=public --permanent --add-port=2857-3857/tcp

firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.5.7" port protocol="tcp" port="857" accept"

firewall-cmd --reload
systemctl reload firewalld
```

#### fpm
```
fpm -s python -t rpm --python-bin python2.7 --python-easyinstall easy_install-2.7 \
--python-install-lib /usr/lib/python2.7/site-packages pysftp
```

#### helm
```bash
helm install --set storage.aws.accessKey="AWS_ACCESS_KEY" --set storage.aws.secret="AWS_SECRET_KEY" --name k8s-app-cluster .
helm upgrade --set storage.aws.accessKey="AWS_ACCESS_KEY" --set storage.aws.secret="AWS_SECRET_KEY" k8s-app-cluster .

helm list
helm delete --purge k8s-app-cluster
```

#### iptables
```bash
cat > /etc/sysconfig/iptables <<EOF
*nat
:PREROUTING ACCEPT [0:0]
:INPUT ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]
-A PREROUTING -d 5.6.7.8 -p tcp --dport 18080 -j DNAT --to 10.8.5.8:8080
-A POSTROUTING -d 10.8.5.0/24 -j SNAT --to 10.8.5.7
-A POSTROUTING -s 10.8.5.0/24 -o eth1 -j MASQUERADE
COMMIT
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 2857:3857 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m udp --dport 53 -j ACCEPT
-A FORWARD -s 10.8.5.0/24 -j ACCEPT
-A FORWARD -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
EOF

iptables-save
```

#### jq
```bash
cat config.json | jq
jq -r '.profiles[] | select(.name == "foobar") | .bazqux' config.json

jq -c '.[]' data.json > data.ndjson
```

#### [kubectl](https://kubernetes.io/zh/docs/reference/kubectl/cheatsheet/)
```bash
kubectl get pods --all-namespaces
kubectl get pods -n kube-system

watch -n 1 kubectl get pods

kubectl get pods -o wide
kubectl describe pods/k8s-app-pod-0
kubectl logs pods/k8s-app-pod-0

kubectl get svc/k8s-app-svc-0
kubectl get svc/k8s-app-svc-0 -o yaml
kubectl describe svc/k8s-app-svc-0

kubectl get pv
kubectl get pvc
kubectl get storageclass/gp2
kubectl get storageclass/gp2 -o yaml
kubectl describe storageclass/gp2

kubectl get sc
kubectl describe sc/gp2
kubectl patch sc gp2 -p '{"allowVolumeExpansion": true}'
kubectl edit pvc/k8s-app-svc-0

kubectl create secret tls k8s-app-ssl-cert --key sslcerts/star.heylinux.com.key --cert sslcerts/star.heylinux.com.crt

kubectl edit configmap aws-auth -n kube-system
kubectl edit svc/k8s-app-ingress

kubectl get configmap
kubectl edit cm/k8s-app-config

KUBECONFIG=k8s-env-1:k8s-env-2:k8s-env-3 kubectl config view --flatten > config
kubectl config current-context
kubectl config use-context k8s-env-1
kubectl config use-context k8s-env-2
kubectl config use-context k8s-env-3
kubectl --kubeconfig ~/.kube/k8s-env-1 get pods

kubectl exec -it k8s-app-pod-0 -- /bin/bash
kubectl exec -i k8s-app-pod-0 -- find /path/to/logdir/ -type d -name "2020*" -exec rm -r {} +

kubectl cp k8s-app-pod-0:/path/to/logdir k8s-app-pod-0_logdir

KUBECTL_EXTERNAL_DIFF=meld kubectl diff -f some-resources.yaml

kubectl run nginx --image=nginx --image-pull-policy=IfNotPresent --dry-run=client -o yaml > nginx-pod.yaml

# decrypt and export a secret
kubectl get secret tls-certs -o json \
| python -c 'import json,sys;obj=json.load(sys.stdin);print(obj["data"]["tls.crt"])' \
| base64 -d > tls.crt

# create ad-hoc-pod.yaml to view the configmap "heylinux-conf-d" key "foo.conf" as actual file format
apiVersion: v1
kind: Pod
metadata:
  name: ad-hoc-pod
spec:
  containers:
    - name: test-container
      image: busybox:latest
      command: [ "/bin/sh","-c","cat /etc/config/*" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: heylinux-conf-d
        items:
        - key: foo.conf
          path: foo.conf
  restartPolicy: Never

# create pod ad-hoc-pod and export the configmap "heylinux-conf-d" key "foo.conf" as configmaps/foo.conf
kubectl apply -f ad-hoc-pod.yaml
kubectl logs ad-hoc-pod > configmaps/foo.conf

# create new configmap "heylinux-conf-d-backup" with configmaps/foo.conf
kubectl create configmap heylinux-conf-d-backup --from-file=configmaps/

# update the configmap "heylinux-conf-d" with configmaps/foo.conf
kubectl create configmap heylinux-conf-d --from-file=configmaps/ -o yaml --dry-run | kubectl apply -f -
```

#### lftp
```bash
lftp -d -e "set ssl:ca-file /etc/ssl/certs/vsftpd.pem" -u username,password 10.1.2.3
---
mput *.tgz
mkdir pkg_dir.20201225
mirror -R pkg_dir.20201225 pkg_dir.20201225

lftp -u username, -e "set sftp:connect-program 'ssh -p 22 -i /home/username/.ssh/id_rsa'" sftp://10.1.2.3
```

#### lvm
```bash
growpart /dev/nvme0n1 3
pvresize /dev/nvme0n1p3
pvscan
free_size=$(vgdisplay | grep Free | awk '{print $5}')
lvextend -L +${free_size} /dev/mapper/VolGroup00-root
xfs_growfs /dev/mapper/VolGroup00-root

pvcreate /dev/nvme1n1
pvresize /dev/nvme1n1
pvscan
vgextend /dev/VolGroup00 /dev/nvme1n1
free_size=$(vgdisplay | grep Free | awk '{print $5}')
lvextend -L +${free_size} /dev/mapper/VolGroup00-root
xfs_growfs /dev/mapper/VolGroup00-root

resize2fs /dev/mapper/VolGroup00-root

xfs_growfs -d /data
xfs_growfs /dev/nvme2n1
```

#### nc
```bash
# tcp
nc -kvl4p 8080
echo -e "hello this is damon" | nc -nv 10.1.2.3 8080
nc -nvz 10.1.2.3 8080

# udp
nc -uvl4p 8053
echo -e "hello this is damon" | nc -nvu 10.1.2.3 8053
nc -nvuz 10.1.2.3 8053

# port forwarding
nc -lk 7017 | nc localhost 27017
socat TCP-LISTEN:7017,reuseaddr,fork TCP:localhost:27017
```

#### pip
```bash
pip install --no-cache-dir -i https://mirrors.aliyun.com/pypi/simple -r requirements.txt
```

#### postgresql
```bash
psql -h 127.0.0.1 -U postgres
---
\l
\c dbname
\d
SELECT id,email,name FROM people ORDER BY id;
DELETE FROM people WHERE id=857;
\du
\dt public.*
CREATE USER dbro WITH ENCRYPTED PASSWORD 'dbpass';
GRANT USAGE ON SCHEMA public TO dbro;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO dbro;
\q
```

#### python
```bash
echo '{"json":"obj"}' | python -m json.tool

echo '{"foo": "lorem", "bar": "ipsum"}' | python -c 'import json,sys;obj=json.load(sys.stdin);print(obj["foo"])'
python -c 'import yaml,sys;obj=yaml.safe_load(sys.stdin);print(list(obj["foo"])[-1])' < /path/to/file.yml
whoami | python -c 'import sys;username=sys.stdin.read().splitlines()[0];print(username)'

python2 -m SimpleHTTPServer 8080
python3 -m http.server 8080
python3 -m http.server 8080 --bind $(ipconfig getifaddr en0)

python - <<EOF
import sys
for i in range(3): print(i)
EOF

python -c "import os
env_lang = os.getenv('LANG')
if ('UTF-8' in env_lang):
    print('INFO: UTF-8 is supported by {0}'.format(env_lang))
else:
    print('WARN: UTF-8 is not supported')"

# remove non utf8 characters
str.encode("ascii", "ignore").decode()

# run Python command with unbuffered output and log to both terminal and file
python3 -u script.py | tee -a out.txt
```

#### route
```bash
route -n

route add default gw 10.8.1.1
route add -net 10.8.5.0 netmask 255.255.255.0 gw 10.8.5.1 dev eth1 metric 101
```

#### rpm
```bash
rpm -qa | grep unzip
rpm -ql unzip
rpm -qi unzip
rpm -qif /usr/bin/unzip
rpm -Uvh unzip-6.0-21.el7.x86_64.rpm
```

#### rsync
```bash
rsync -a --exclude "plugins/linux_amd64" repo/terraform-iac/ backups/terraform-iac.$(date +%Y%m%d)

rsync -e "ssh -p 22" --timeout=10 --delete --log-file=logs/rsync.$(date +%Y%m%d).log \
-ravz backups user@hostname:/opt/backups
```

#### sed
```bash
sed -i --follow-symlinks '/max_log_file/ s/8/100/' /etc/audit/auditd.conf
sed -i --follow-symlinks '/^max_log_file_action/ s/=.*/= ROTATE/' /etc/audit/auditd.conf

sed -i --follow-symlinks '/GRUB_CMDLINE_LINUX/ s/\"$/ ipv6.disable=1 audit=1\"/' /etc/sysconfig/grub
sed -i --follow-symlinks '/^AllowUsers/ s/$/ ec2-user/' /etc/ssh/sshd_config

sed -i.bak '/port=0/d' kube-scheduler.yaml
sed -i "/^data:/a \  ssl_crt: ${ssl_crt_base64}" anaconda-enterprise-certs.yml

sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

sed -i '/kexalgorithms/d' /etc/ssh/sshd_config

sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://mirror.centos.org|baseurl=https://vault.centos.org|g' \
    -i.bak \
    /etc/yum.repos.d/CentOS-*.repo
```

#### setfacl
```bash
mkdir pkg_dir
chown root:root pkg_dir
chmod 770 pkg_dir

setfacl -dm u:tom:rwx -dm g:ops:rwx pkg_dir
setfacl -m u:tom:rwx -m g:ops:rwx pkg_dir

mkdir pkg_dir/sub_dir
touch pkg_dir/sub_dir/file.txt

getfacl -R pkg_dir

setfacl -b pkg_dir
```

#### ssh
```bash
# ssh tunneling and port forwarding
# request -> local 10.8.5.7:18080 -> remote_host 5.6.7.8:22 -> dest 10.1.2.8:8080
ssh -i ${sshkey} -p ${sshport} -f -NTL ${local_ip}:${local_port}:${dest_ip}:${dest_port} ${sshuser}@${remote_host}
ssh -i /path/to/sshkey -p 22 -f -NTL 10.8.5.7:18080:10.1.2.8:8080 sshuser@5.6.7.8

# request -> remote_host_lan 10.1.2.7:8080 -> remote_host 5.6.7.8:22 -> local 10.8.5.7:18080
# enable GatewayPorts option in sshd_config
ssh -i ${sshkey} -p ${sshport} -f -NTR ${remote_lan_ip}:${remote_port}:${local_ip}:${local_port} ${sshuser}@${remote_host}
ssh -i /path/to/sshkey -p 22 -f -NTR 10.1.2.7:8080:10.8.5.7:18080 sshuser@5.6.7.8

# ssh jumpserver
## jump over proxy to dest_host 10.8.5.7:22
## support multiple proxies: user1@proxy1:port1,user2@proxy2:port2
ssh -i /path/to/sshkey -p 22 -J jumpuser@proxy.example.org:10022 user@10.8.5.7

## /etc/ssh/sshd_config on proxy.example.org
Match User jumpuser
  PermitTTY no
  X11Forwarding no
  PermitTunnel no
  GatewayPorts no
  ForceCommand /usr/sbin/nologin

## ~/.ssh/config on client
Host 10.8.5.*
    ProxyJump jumpuser@proxy.example.org:10022

# sshkey
ssh-keygen -t rsa -b 4096 -C "mcsrainbow@heylinux.com" -f mcsrainbow_id_rsa
ssh-copy-id -i /path/to/ssh-pub-key ssh-user@10.8.5.8

# sshfs
sshfs -o allow_other,port=22,IdentityFile=/path/to/ssh-key user@10.8.5.8:/path/to/dir /path/to/mnt/dir
```

#### systemd
```bash
cat > /etc/systemd/system/nifi.service <<EOF
[Unit]
Description=NiFi Service
After=syslog.target

[Service]
Type=forking
ExecStart=/opt/nifi/bin/nifi.sh start
ExecStop=/opt/nifi/bin/nifi.sh stop
User=ec2-user
LimitNOFILE=65536
LimitNPROC=4096
LimitFSIZE=infinity

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable nifi.service

systemctl start nifi
systemctl stop nifi
systemctl status nifi
```

#### tar
```bash
tar --exclude=pkg_dir/pkg_dir_file1.txt --exclude=pkg_dir/sub_dir1 -cvzpfh pkg_dir.$(date +%Y%m%d).tgz pkg_dir

tar tzvpf pkg_dir.20201225.tgz

mkdir pkg_dir.20201225
tar xzpf pkg_dir.20201225.tgz -C pkg_dir.20201225
```

#### tcpdump
```bash
tcpdump -i any port 8080
tcpdump -i any tcp port 8080 -w tcp_8080.cap
tcpdump -r tcp_8080.cap

tcpdump -i any icmp

tcpdump -i any -an 'src portrange 10021-11021 or src port 21'

tcpdump -i any host 10.1.2.2
tcpdump -i any src host 10.1.2.2

tcpdump -i any src net 10.1.0.0/24
tcpdump -i any dst net 10.1.0.0/24

tcpdump -i any -nvX src 10.1.2.2 and dst port 22
tcpdump -i any -nvX src net 192.168.0.0/16 and dst net 10.0.0.0/8 or 172.16.0.0/16

tcpdump -i any -w traffic.pcap host 8.5.7.3 and port 8080
tcpdump -i any -n -X host 8.5.7.3 and port 8080
```

#### uv
```bash
# install uv
pip install uv
uv venv .venv --python=python3.10

uv pip list
uv sync

uv install

# pip
source .venv/bin/activate

uv pip list
uv pip freeze > requirements.txt

uv pip install --no-cache-dir -i https://mirrors.aliyun.com/pypi/simple -r requirements.txt

# run script.py without activating the venv
uv run python script.py
```

#### wget
```bash
wget -r --no-parent https://repo.anaconda.com/pkgs/r/linux-64/

wget -m -np -nH --cut-dirs=2 -R --regex-type pcre --accept-regex '(.*s3fs.*)' https://repo.anaconda.com/pkgs/main/linux-64/

wget --header "PRIVATE-TOKEN: <your_access_token>" \
"https://jihulab.com/api/v4/projects/857857/repository/files/folder%2Fpackage.tar/raw?ref=main" \
-O package.tar
```

#### yq
```bash
kustomize build overlays/development | yq eval --prettyPrint --indent=2
yq 'select(.kind == "Ingress" and .metadata.name == "foobar-ingress") | .spec.rules[].http.paths[].path' bu-project-foobar.yaml
```

#### yum
```bash
mkdir /tmp/nginx_rpms
yum install --enablerepo epel --downloadonly --downloaddir=/tmp/nginx_rpms nginx nginx-mod-stream

cd /tmp/nginx_rpms
yum localinstall *.rpm --disablerepo='*'
```

#### zip
```bash
zip --symlinks -r9 pkg_dir.$(date +%Y%m%d).zip pkg_dir -x "pkg_dir/pkg_dir_file1.txt" "pkg_dir/sub_dir1/*"

unzip -l pkg_dir.20201225.zip

zip -r9 pkg_dir.20201225.zip pkg_dir/sub_dir1

mkdir pkg_dir.20201225
unzip pkg_dir.20201225.zip -d pkg_dir.20201225

# zip all files including hidden, excluding .DS_Store and archive itself
cd /path/to/directory
zip -r archive.zip . -x ".DS_Store" "archive.zip"
```

#### misc
```bash
# change password in one command line
echo "password" | passwd --stdin username

# create a default home directory and update user groups
mkhomedir_helper username
chmod 700 /home/username
usermod -s /bin/bash username
usermod -a -G adm,sudo,docker username

# rename filename suffix svg.png as png
\ls *.svg.png | xargs basename -s .svg.png | xargs -I {} mv {}.svg.png {}.png

# update file in one command line
cat <<EOF | sudo tee -a /etc/modules-load.d/k8s.conf
br_netfilter
EOF

# processes order by mem/cpu desc
ps -eo pid,ppid,pmem,pcpu,cmd --sort=-pmem
ps -eo pid,ppid,pmem,pcpu,cmd --sort=-pcpu

# disable history of current shell
set +o history

# keep alive
sleep infinity

# list in natural order
ls -lvh

# delete a single command with id number from shell history
history -d 857

# watch usage metrics of cpu, memory, disk io, network
vmstat -n 1
iostat -dxm 1
sar -n DEV 1
htop
iotop
iftop

# bandwidth testing
iperf3

# generate a list of checksums for any file that matches *.csv
md5sum *.csv > checklist.chk
# run through the list to check them
md5sum -c checklist.chk

# create a symlink
ln -sfT app-1.1 app

# expansion of variables inside single quotes
# cat echo.sh
# echo $1
dst_url="/path/to/dst_url"
./echo.sh '/path/to/src_url/(.*) -> '"$dst_url"'/$1'
# /path/to/src_url/(.*) -> /path/to/dst_url/$1

# error handling in bash scripts
set -e            # when a command fails, set -e ensures that the entire script exits instead of continuing to the next line
set -u            # treat unset variables as errors and exit immediately
set -f            # disable filename expansion (globbing) when encountering characters like *, ?, etc.
set -o pipefail   # cause a pipeline (e.g., command1| command 2 | grep foo) to fail if any command within the pipeline errors
set -euo pipefail # both -e, -u and -o pipefail

# rename files by removing the .j2 suffix
for i in $(find . -type f -name "*.j2"); do mv "$i" "${i%.j2}";done
```
