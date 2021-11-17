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
[docker](https://github.com/mcsrainbow/notes/tree/master/docker)
[du](#du)
[fallocate](#fallocate)
[find](#find)
[firewalld](#firewalld)
[git](https://github.com/mcsrainbow/notes/blob/master/git/git_notes.md)
[helm](#helm)
[iptables](#iptables)
[kubectl](#kubectl)
[lftp](#lftp)
[lvm](#lvm)
[misc](#misc)
[nc](#nc)
[openssl](https://github.com/mcsrainbow/notes/tree/master/certs)
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
[wget](#wget)
[yum](#yum)
[zip](#zip) 
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
```

#### aws
```bash
$(aws ecr get-login --no-include-email --region ap-east-1 --registry-ids 857857857857)
$(aws ecr get-login --no-include-email --region ap-east-1)

docker_ecr_password=$(aws ecr get-login-password --region ap-east-1)
docker login --username AWS --password $docker_ecr_password 857857857857.dkr.ecr.ap-east-1.amazonaws.com

aws s3 ls --summarize --recursive s3://bucket-name
```

#### curl
```bash
curl -sL https://www.opscode.com/chef/install.sh | sudo bash

curl --fail -u username:password --upload-file heylinux-app.rpm http://nexus.heylinux.com/repository/devops/rpms/

curl --progress-bar -o /tmp/heylinux-app.rpm http://nexus.heylinux.com/repository/devops/rpms/heylinux-app.rpm
```

#### conda
```bash
bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/miniconda

source /opt/miniconda/etc/profile.d/conda.sh
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
```

#### cron
```bash
echo damonguo >> /etc/cron.allow

crontab -u damonguo -l
---
## minute hour day-of-month month day-of-week(0/7=sun)
##  0-59  0-23     1-31     1-12  0-7/mon,tue,wed,thu,fri,sat,sun
#OPS-857: run /opt/bin/run.py every 5 minutes and log all outputs and alert if fails
MAILTO="ops-alerts@heylinux.com"
*/5 * * * * ps aux | grep /opt/bin/run.py | grep -v grep || /opt/bin/run.py >> /opt/logs/run.out 2> >(tee -a /opt/logs/run.err >&2)
```

#### du
```bash
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
find . -maxdepth 1 -type f -size +100M -exec ls -lh {} + | sort -k2 | awk '{print $5"\t"$NF}'
find -type f -size +100M -exec ls -lh {} + | awk '{print $5"\t"$NF}' | sort -rn
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

#### [kubectl](https://kubernetes.io/zh/docs/reference/kubectl/cheatsheet/)
```bash
kubectl get pods --all-namespaces
kubectl get pods -n kube-system
kubectl edit daemonset/aws-node -n kube-system
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

kubectl create secret tls k8s-app-ssl-cert --key sslcerts/star.heylinux.com.key --cert sslcerts/star.heylinux.com.crt

kubectl edit configmap aws-auth -n kube-system

kubectl edit svc/k8s-app-ingress

kubectl get configmap
kubectl edit cm/k8s-app-config

kubectl config current-context
kubectl config use-context k8s-env-1
kubectl config use-context k8s-env-2

kubectl exec -it k8s-app-pod-0 -- /bin/bash
kubectl exec -i k8s-app-pod-0 -- find /path/to/logdir/ -type d -name "2020*" -exec rm -r {} +

kubectl cp k8s-app-pod-0:/path/to/logdir k8s-app-pod-0_logdir

KUBECTL_EXTERNAL_DIFF=meld kubectl diff -f some-resources.yaml
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

#### misc
```bash
echo "password" | passwd --stdin username

\ls *.svg.png | xargs basename -s .svg.png | xargs -I {} mv {}.svg.png {}.png

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF


```

#### nc
```bash
nc -kvl4p 8080
echo -e "hello this is damon" | nc -nv 10.1.2.3 8080
nc -nvz 10.1.2.3 8080

nc -uvl4p 8053
echo -e "hello this is damon" | nc -nvu 10.1.2.3 8053
nc -nvuz 10.1.2.3 8053
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
python -c 'import yaml,sys;obj=yaml.safe_load(sys.stdin);print(list(obj["foo"])[-1])' < /path/to/file.yml)
whoami | python -c 'import sys;username=sys.stdin.read().splitlines()[0];print(username)'

python2 -m SimpleHTTPServer 8080
python -m http.server 8080

python - <<EOF
import sys
for i in range(3): print(i)
EOF
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
rsync -e "ssh -p 22" --timeout=10 --delete --log-file=logs/rsync.$(date +%Y%m%d).log -ravz backups user@hostname:/opt/backups
```

#### sed
```bash
sed -i --follow-symlinks '/max_log_file/ s/8/100/' /etc/audit/auditd.conf
sed -i --follow-symlinks '/^max_log_file_action/ s/=.*/= ROTATE/' /etc/audit/auditd.conf

sed -i --follow-symlinks '/GRUB_CMDLINE_LINUX/ s/\"$/ ipv6.disable=1 audit=1\"/' /etc/sysconfig/grub
sed -i --follow-symlinks '/^AllowUsers/ s/$/ ec2-user/' /etc/ssh/sshd_config

sed -i.bak '/port=0/d' kube-scheduler.yaml
sed -i "/^data:/a \  ssl_crt: ${ssl_crt_base64}" anaconda-enterprise-certs.yml
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
ssh -i ${ssh_key} -p ${ssh_port} -l ${ssh_user} -f -N -T -L ${local_ip}:${local_port}:${dest_ip}:${dest_port} ${ssh_host}
ssh -i /path/to/ssh-key -p 22 -l ssh-user -f -N -T -L 10.8.5.7:18080:10.1.2.3:8080 5.6.7.8

ssh-copy-id -i /path/to/ssh-pub-key ssh-user@10.8.5.8
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
```

#### wget
```bash
wget -r --no-parent https://repo.anaconda.com/pkgs/r/linux-64/

wget -m -np -nH --cut-dirs=2 -R --regex-type pcre --accept-regex '(.*s3fs.*)' https://repo.anaconda.com/pkgs/main/linux-64/
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
```
