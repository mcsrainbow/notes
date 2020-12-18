#### Index
<!--ts-->
[aws](#awk) [curl](#curl) [conda](#conda) [helm](#helm) [misc](#misc) [python](#python) [rsync](#rsync) [sed](#sed)
<!--te-->

#### awk
```bash
awk -F: '($3<1000){print $1}' /etc/passwd

aws ecr describe-repositories --output text | awk '($1~/^REPO/){print $NF}'
```

#### aws
```bash
$(aws ecr get-login --no-include-email --region ap-east-1 --registry-ids 567898765432)
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

#### helm
```bash
kubectl edit configmap aws-auth -n kube-system
```

#### misc
```bash
echo "password" | passwd --stdin username
```

#### python
```bash
echo '{"json":"obj"}' | python -m json.tool
```

#### rsync
```bash
rsync -a --exclude "subfolder/of/sourcedir" /path/to/sourcedir/ /path/to/destdir.$(date +%Y%m%d)
```

#### sed
```bash
sed -i --follow-symlinks '/max_log_file/ s/8/100/' /etc/audit/auditd.conf

sed -i --follow-symlinks '/GRUB_CMDLINE_LINUX/ s/\"$/ ipv6.disable=1 audit=1\"/' /etc/sysconfig/grub
sed -i --follow-symlinks '/^AllowUsers/ s/$/ ec2-user/' /etc/ssh/sshd_config
```
