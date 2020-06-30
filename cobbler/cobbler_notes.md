基于Cobbler的自动化安装实践
---

#### 什么是PXE

基本上所有的网卡，都有PXE芯片，其512字节的容量中存放了DHCP和TFTP的客户端，能够与DHCP Server与TFTP Server通讯

PXE工作流程如下：

```text
1. 向DHCP Server请求IP地址和PXE初始化文件位置
2. 向TFTP服务器下载pxelinux.0初始化文件，pxelinux.0告诉PXE要下载的配置文件是pxelinux.cfg目录下的default文件
3. 向TFTP服务器请求pxelinux.cfg目录下的default文件
4. 向TFTP服务器请求default文件中指定的vmlinuz，即可引导的，压缩的Linux内核
5. 向TFTP服务器请求initrd.img，即最小化的Linux系统映象
6. 启动Linux内核与系统映象，执行default文件中指定的kickstart文件进行自动化安装配置
```

#### PXE与Kickstart部署流程

```text
1. 准备安装源
2. 准备Kickstart文件
3. 准备TFTP Server
4. 准备DHCP Server
5. 开始自动化安装
```

##### 挂载镜像文件CentOS-7-x86_64-DVD-2003.iso

```text
mkdir /mnt/cdrom
mount /dev/cdrom /mnt/cdrom/

yum install -y httpd

cd /var/www/html
mkdir CentOS-7.8-x86_64
cp -a /mnt/cdrom/* /var/www/html/CentOS-7.8-x86_64/
```

##### 准备Kickstart.cfg

```text
vim /var/www/html/CentOS-7.8-x86_64/CentOS-7.8-x86_64-Kickstart.cfg
```

```bash
# Kickstart Configuration by Damon Guo
#platform=x86, AMD64, or Intel EM64T
#System language
lang en_US
#System keyboard
keyboard us
#System timezone
timezone Asia/Shanghai
#Root password
rootpw --iscrypted $1$example$I.i3m.26O7QYNja8p5Cj9.0
#Use text mode install
text
#Install OS instead of upgrade
install
#Use HTTP installation Media
url --url=http://172.16.181.171/CentOS-7.8-x86_64
#System bootloader configuration
bootloader --location=mbr --driveorder=sda --append="net.ifnames=0 biosdevname=0"
#Clear the Master Boot Record
zerombr
#Partition clearing information
clearpart --all --initlabel
#Disk partitioning information
part /boot --fstype=xfs --size=1024
part swap --asprimary --fstype="swap" --size=1024
part / --fstype=xfs --size=1 --grow
#System authorization information
auth --useshadow --enablemd5
#Network information
network --bootproto=dhcp --device=eth0 --onboot=on --activate
#Reboot after installation
reboot
#Firewall configuration
firewall --disabled
#SELinux configuration
selinux --disabled
#Service configuration
services --disabled=postfix
#Do not configure XWindows
skipx
#Package install information
%packages
@base
@core
vim
bash-completion
sysstat
ntp
bind-utils
net-tools
openssl-devel
zlib-devel
screen
mlocate
%end
```

##### 准备TFTP Server

```text
# yum install -y tftp-server xinetd
# vim /etc/xinetd.d/tftp
```

```bash
# default: off
# description: The tftp server serves files using the trivial file transfer \
#       protocol.  The tftp protocol is often used to boot diskless \
#       workstations, download configuration files to network-aware printers, \
#       and to start the installation process for some operating systems.
service tftp
{
        socket_type             = dgram
        protocol                = udp
        wait                    = yes
        user                    = root
        server                  = /usr/sbin/in.tftpd
        server_args             = -s /var/lib/tftpboot
        disable                 = no
        per_source              = 11
        cps                     = 100 2
        flags                   = IPv4
}
```

```
# yum install -y syslinux
# mkdir /var/lib/tftpboot/pxelinux
# cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/pxelinux
# cp /mnt/cdrom/isolinux/* /var/lib/tftpboot/pxelinux/
# mkdir /var/lib/tftpboot/pxelinux/pxelinux.cfg
# cp /mnt/cdrom/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux/pxelinux.cfg/default

# vim /var/lib/tftpboot/pxelinux/pxelinux.cfg/default
label ks
  menu label ^Auto Install CentOS 7
  kernel vmlinuz
  append initrd=initrd.img ip=dhcp inst.repo=http://172.16.181.171/CentOS-7.8-x86_64 inst.ks=http://172.16.181.171/CentOS-7.8-x86_64/CentOS-7.8-x86_64-Kickstart.cfg
```

##### 准备DHCP Server

```bash
subnet 172.16.181.0 netmask 255.255.255.0 {
  range 172.16.181.100 172.16.181.150;
  option routers 172.16.181.2;
  option subnet-mask 255.255.255.0;
  default-lease-time 600;
  max-lease-time 7200;
  # 指向到TFTP Server IP
  next-server 172.16.181.171;
  # 设置TFTP Client默认下载的文件
  filename "pxelinux/pxelinux.0";
}
```

##### 开始自动化安装

使用VMWare时注意将`虚拟网络编辑器`中的`NAT模式`网络的`使用本地DHCP服务将IP地址分配给虚拟机`选项打开

使用CentOS7的虚拟机时，需要将内存设置为`2048M`，否则无法启动自动化安装程序


#### Cobbler安装配置

##### Cobbler的功能

```text
使用一个预定义的模板来配置DHCP服务
将一个存储库建立镜像，用于注册为一个操作系统源
在DHCP配置文件中为需要安装的服务器创建单独的条目，匹配相应的IP与MAC地址属性
在TFTP服务目录下创建合适的PXE文件
重新启动DHCP服务并应用变更
重新启动机器电源并开始安装
```

##### 安装Cobbler

```bash
yum install -y httpd dhcp tftp cobbler xinetd pykickstart python-ctypes

systemctl restart httpd
systemctl start cobblerd

cobbler get-loaders
```

##### 检查Cobbler所需的配置内容

```bash
cobbler check
```

##### 修改next_server与server为Cobbler Server IP

```bash
sed -i '/server:/ s/127.0.0.1/172.16.181.171/' /etc/cobbler/settings
```

```bash
systemctl enable rsyncd
yum install -y debmirror fence-agents

sed -i '/@dists=/ s/@dists/#@dists/' /etc/debmirror.conf
sed -i '/@arches=/ s/@arches/#@arches/' /etc/debmirror.conf
```

##### 生成并替换加盐加密后的密码字符串

```text
# openssl passwd -1 -salt 'your-salt-string' 'heydevops'
$1$your-sal$IoGOGbTKgUjTShc9i9gCV/

# vim /etc/cobbler/settings
default_password_crypted: '$1$your-sal$IoGOGbTKgUjTShc9i9gCV/'
```

```bash
systemctl restart cobblerd
cobbler check
```

##### 使用Cobbler管理DHCP

```text
# vim /etc/cobbler/settings
manage_dhcp: 1
```

```bash
sed -i 's/192.168.1.1/172.16.181.2/g' /etc/cobbler/dhcp.template
sed -i 's/192.168.1/172.16.181/g' /etc/cobbler/dhcp.template

systemctl restart cobblerd
cobbler sync
```

##### 导入镜像

```bash
cobbler import --path=/mnt/cdrom --name=CentOS-7.8-x86_64 --arch=x86_64
ls /var/www/cobbler/ks_mirror/CentOS-7.8-x86_64/

cat /etc/httpd/conf.d/cobbler.conf

cobbler list
cobbler profile report

cat /var/lib/cobbler/kickstarts/sample_end.ks

cobbler profile edit --name=CentOS-7.8-x86_64 --kickstart=/var/lib/cobbler/kickstarts/CentOS-7.8-x86_64-Cobbler.cfg

cobbler profile edit --name=CentOS-7.8-x86_64 --kopts='net.ifnames=0 biosdevname=0'

cobbler profile report
cobbler sync
```

##### 使用Koan进行自动化重装

在需要重装的服务器上安装Koan并重装自己

```bash
yum install -y epel-release
yum install -y koan

koan --server=172.16.181.171 --list=profiles
koan --replace-self --server=172.16.181.171 --profile=CentOS-7.8-x86_64
```

##### Cobbler Web

Cobbler Web使用Django开发
```bash
yum install -y cobbler-web
systemctl restart httpd
```

```bash
htdigest /etc/cobbler/users.digest "Cobbler" cobbler
Changing password for user cobbler in realm Cobbler
New password: cobbler
Re-type new password: cobbler
```

访问`https://172.16.181.171/cobbler_web/`

#### Cobbler深入理解

##### 添加Cobbler Repo

```text
cobbler repo add --name=saltstack-2018.3-rhel7-x86_64 --mirror=https://repo.saltstack.com/yum/redhat/7/x86)64/2018.3

cobbler repo list
cobbler reposync

cobbler profile edit --name=CentOS-7.4-x86_64 --repos="saltstack-2018.3-rhel7-x86_64"

cobbler sync
```

##### 服务器上架流程

```text
1. 服务器上架
2. 验收，资产记录，标签，EXCEL表记录MAC地址
3. RAID，IPMI配置
4. 录入到资产管理系统
5. MAC地址与DHCP IP设置
6. IP地址，主机名，DNS，网关等设置
7. 安装配置管理工具Agent，自动化配置
```

```bash
cobbler system add \
--name=test-node1 \
--mac=00:50:56:23:1E:B5 \
--profile=CentOS-7.8-x86_64 \
--ip-address=172.16.181.101 \
--subnet=255.255.255.0 \
--gateway=172.16.181.2 \
--interface=eth0 \
--static=1 \
--hostname=test-node1.example.org \
--name-servers="172.16.181.2" \
--kickstart=/var/lib/cobbler/kickstarts/CentOS-7.8-x86_64-Cobbler.cfg

cobbler sync

cat /etc/dhcp/dhcpd.conf
```

##### Cobbler网卡绑定

```bash
cobbler system edit \
--name=test-node2 \
--interface=bond0 \
--interface-type=bond \
--bonding-opts="miimon=100 mode=1" \
--ip-address=172.16.181.102 \
--subnet=255.255.255.0 \
--gateway=172.16.181.2 \
--static=1
```

##### Cobbler Python API实现

```python
import xmlrpclib
server = xmlrpclib.Server("http://172.16.181.171/cobbler_api")
print server.get_distros()
print server.get_profiles()
print server.get_systems()
print server.get_images()
print server.get_repos()

token = server.login("cobbler","cobbler")
print(token)
```

##### Cobbler电源控制与Anamon安装过程监控

https://cobbler.github.io/manuals/2.8.0/
