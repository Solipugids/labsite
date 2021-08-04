---
title: Jian Yang and Monica Hall Win the Best Paper Award at Wowchemy 2020
date: 2020-12-02
image:
  focal_point: "top"
---

用多台centos服务器组成一个计算集群，其中一个作为登录节点，共享用户目录和文件目录，文件目录使用RAID0进行管理，弹性调整空间容量，进行统一的用户管理。其他的计算节点使用最小化安装，预配了4个core和8G内存。

<!--more-->

整个配置流程参考了从其他地方看到的两个文档，“[SGE部署](http://www.chenlianfu.com/?p=2441)”和“[SGE安装部署完整过程](https://www.bilibili.com/read/cv9991574)”。
登录节点的操作系统使用centos的官方版本`CentOS-7-x86_64-Everything-1804.iso`，安装成`server with GUI`方便管理。主机的ip地址是11.11.11.11，默认主机名为localhost.localdomain。
###### 修改主机名称

```bash
sudo hostnamectl set-hostname master
```
修改后不用重启服务器，重新登录后立即生效。可以在`/proc/sys/kernel/hostname`和`/etc/hostname`文件中看到修改成功。
###### 修改网络名称
 `/etc/sysconfig/network`
```bash
NETWORKING=yes
HOSTNAME=master
```
`/etc/hosts`
```bash
11.11.11.11     master
```
###### 修改SELinux状态
`/etc/selinux/config`
```bash
SELINUX=permissive
```
重新启动登录节点服务器，`getenforce`查看修改生效。
###### 计算节点ssh登录认证
###### 搭建NFS服务
准备一个共享目录/share
```bash
sduo mkdir /share
sudo chmod 1777 /share
```
把目录共享给指定IP段内的计算机，在`/etc/exports`内添加
```bash
/share	11.11.11.0/24(rw,sync,no_root_squash,no_subtree_check)
```
NFS服务需要开放一些端口，在`/etc/sysconfig/nfs`中修改
```bash

LOCKD_TCPPORT=32803
LOCKD_UDPPORT=32769
MOUNTD_PORT=892
STATD_PORT=662
STATD_OUTGOING_PORT=2020

```
在防火墙上开启端口
```bash
sudo firewall-cmd --add-source="10.91.15.0/24"
sudo firewall-cmd --add-port=32803/tcp
sudo firewall-cmd --add-port=32803/udp
sudo firewall-cmd --add-port=32769/tcp
sudo firewall-cmd --add-port=32769/udp
sudo firewall-cmd --add-port=892/tcp
sudo firewall-cmd --add-port=892/udp
sudo firewall-cmd --add-port=662/tcp
sudo firewall-cmd --add-port=662/udp
sudo firewall-cmd --add-port=2020/tcp
sudo firewall-cmd --add-port=2020/udp
firewall-cmd --runtime-to-permanent
```
开启RPC服务，让客户端可以连接上正确的端口
```bash
```
开启rpcbind和nfs服务
```bash
systemctl start rpcbind
systemctl start nfs
```
设置为开机启动
```bash
systemctl enable rpcbind
systemctl enable nfs
```
在`/etc/fstab`设置自动挂载

###### 搭建NIS服务
安装NIS
```bash
sudo yum install ypserv.x86_64 ypbind.x86_64
```
修改NIS域名`/etc/sysconfig/network`

修改`/etc/sysconfig/yppasswdd`
```bash
YPPASSWDD_ARGS="--port 1012"
```
修改防火墙配置
```bash
sudo firewall-cmd --add-port=1011/tcp
sudo firewall-cmd --add-port=1011/udp
sudo firewall-cmd --add-port=1012/tcp
sudo firewall-cmd --add-port=1012/udp
```
启动NIS服务和NIS密码修改服务
```bash
sudo systemctl start ypserv
sudo systemctl start yppasswdd
```
开机启动
```bash
sudo systemctl status ypserv
sudo systemctl status yppasswdd
```