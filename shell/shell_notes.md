#### sed

```bash
sed -i --follow-symlinks '/max_log_file/ s/8/100/' /etc/audit/auditd.conf

sed -i --follow-symlinks '/GRUB_CMDLINE_LINUX/ s/\"$/ ipv6.disable=1 audit=1\"/' /etc/sysconfig/grub
sed -i --follow-symlinks '/^AllowUsers/ s/$/ ec2-user/' /etc/ssh/sshd_config
```

#### awk

```bash
awk -F: '($3 < 1000) {print $1 }' /etc/passwd
```
