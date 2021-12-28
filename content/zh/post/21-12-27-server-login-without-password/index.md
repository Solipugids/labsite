---
title: 在服务器上安装不同版本的R和配置Rstudio server
image:
  focal_point: "top"
---

Rstudio的服务器版本安装过程，包括R和Rstudio。
<!--more-->

#### 安装R
启动`EPEL`
```bash
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm 
```
指定要安装的`R`的版本
```bash
export R_VERSION=4.1.2
```
ps：如果想安装旧版本的R的话，就在这里指定版本号。

从`rstudio`服务器下载和安装`R`
```bash
curl -O https://cdn.rstudio.com/r/centos-7/pkgs/R-${R_VERSION}-1-1.x86_64.rpm
sudo yum install R-${R_VERSION}-1-1.x86_64.rpm
```
验证是否安装成功
```bash
/opt/R/${R_VERSION}/bin/R --version
```
为了保证`R`的安装路径在默认的系统环境变量下，可以创建一个`symbolic links`
```bash
sudo ln -s /opt/R/${R_VERSION}/bin/R /usr/local/bin/R
sudo ln -s /opt/R/${R_VERSION}/bin/Rscript /usr/local/bin/Rscript
```
#### 安装`Rstudio server`
```bash
wget https://download2.rstudio.org/server/centos7/x86_64/rstudio-server-rhel-2021.09.1-372-x86_64.rpm
sudo yum install rstudio-server-rhel-2021.09.1-372-x86_64.rpm
```
检查`Rstudio server`是否安装完成
```bash
sudo rstudio-server verify-installation
```
然后启动`rstudio-server`，查看服务器状态用`status`，重启用`restart`
```bash
sudo rstudio-server start
```
这时候应该还不能访问到`rstudio`，因为防火墙还在屏蔽`rstudio-server`默认8787端口的访问。正确的方法是把8787端口开放。现在的`centos`已经采用`firewalld`来控制防火墙，有的服务器版本用的是`iptable`。这里用`firewalld`的配置来举个例子：
```bash
sudo firewall-cmd --zone=public --add-port=8787/tcp --permanent
```
可以用`remove-port`删除开放的端口，也可以查看端口目前的打开状态`query-port`。
打开端口以后，就可以正常访问到Rstudio了。可以直接用服务器开设的账号进行登录。