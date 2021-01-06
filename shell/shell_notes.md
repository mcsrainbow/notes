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
[kubectl](#kubectl)
[lftp](#lftp)
[misc](#misc)
[nc](#nc)
[openssl](https://github.com/mcsrainbow/notes/tree/master/certs)
[postgresql](#postgresql)
[python](#python)
[rpm](#rpm)
[rsync](#rsync)
[sed](#sed)
[systemd](#systemd)
[tar](#tar)
[tcpdump](#tcpdump)
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
```

#### curl
```bash
curl --fail -u username:password --upload-file heylinux-app.rpm http://localhost/repository/heylinux-repo/rpms/
```

#### conda
```bash
bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/miniconda

source /opt/miniconda/etc/profile.d/conda.sh
conda create -n heylinux python=3.8

conda activate heylinux
conda list
conda install paramiko
conda install PyYAML

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

#### kubectl
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

kubectl config current-context
kubectl config use-context k8s-env-1
kubectl config use-context k8s-env-2

kubectl exec -it k8s-app-pod-0 -- /bin/bash
kubectl exec -i k8s-app-pod-0 -- find /path/to/logdir/ -type d -name "2020*" -exec rm -r {} +

kubectl cp k8s-app-pod-0:/path/to/logdir k8s-app-pod-0_logdir
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

#### misc
```bash
echo "password" | passwd --stdin username
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
select id,email,name from people order by id;
delete from people where id=857;
\q
```

#### python
```bash
echo '{"json":"obj"}' | python -m json.tool

python2 -m SimpleHTTPServer 8080
python -m http.server 8080
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

sed -i --follow-symlinks '/GRUB_CMDLINE_LINUX/ s/\"$/ ipv6.disable=1 audit=1\"/' /etc/sysconfig/grub
sed -i --follow-symlinks '/^AllowUsers/ s/$/ ec2-user/' /etc/ssh/sshd_config
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

#### zip
```bash
zip --symlinks -r9 pkg_dir.$(date +%Y%m%d).zip pkg_dir -x "pkg_dir/pkg_dir_file1.txt" "pkg_dir/sub_dir1/*"

unzip -l pkg_dir.20201225.zip

zip -r9 pkg_dir.20201225.zip pkg_dir/sub_dir1

mkdir pkg_dir.20201225
unzip pkg_dir.20201225.zip -d pkg_dir.20201225
```
