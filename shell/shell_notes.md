#### Index
<!--ts-->
[awk](#awk) [aws](#aws) [curl](#curl) [conda](#conda) [helm](#helm) [kubectl](#kubectl) [misc](#misc) [python](#python) [rsync](#rsync) [sed](#sed)
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
rsync -a --exclude "plugins/linux_amd64" repo/terraform-iac/ backups/terraform-iac.$(date +%Y%m%d)
```

#### sed
```bash
sed -i --follow-symlinks '/max_log_file/ s/8/100/' /etc/audit/auditd.conf

sed -i --follow-symlinks '/GRUB_CMDLINE_LINUX/ s/\"$/ ipv6.disable=1 audit=1\"/' /etc/sysconfig/grub
sed -i --follow-symlinks '/^AllowUsers/ s/$/ ec2-user/' /etc/ssh/sshd_config
```
