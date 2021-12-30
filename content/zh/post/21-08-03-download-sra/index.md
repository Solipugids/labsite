---
title: 批量下载SRA数据
date: 2021-08-03
image:
  focal_point: "top"
---


<!--more-->
首先从NCBI下载最新版本的[NCBI SRA Toolkit](https://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=software)（链接可能会失效）。页面上提供了很多种系统下的工具下载方式，因为是安装在CentOS服务器上面的，有管理员权限，所以我选择了`Cloud - yum install script`的方式。这样安装后的`SRA Toolkit`可以提供给服务器上的所有用户使用。如果是在自己目录下用，可以选择`non-sudo tar archive`的方式。

###### 安装SRA Toolkit
```bash
wget https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.11.0/setup-yum.sh
sudo sh setup-yum.sh
```
默认安装路径是`／usr／local／ncbi`。安装完成后需要先手动添加到路径下
```bash
source /etc/profile.d/sra-tools.sh
```
###### 下载数据

使用一篇发表在Cancer Cell上[文章](https://doi.org/10.1016/j.ccell.2019.02.001)中附带的数据作为例子，来演示如何进行NCBI上大文件的批量下载。文章中附带数据的索引号是SRP157974，NCBI检索发现有727条记录。先把这些记录保存下来，检索结果的右上角有一个`Send to`，`Choose Destination`选择`File`，`Format`选择`Accession List`。这样就把检索结果的索引号保存到`SraAccList`。使用`SRA Toolkit`里的`prefetch`命令可以批量下载，如果下载时间比较长，用`nohup`挂载到后台，把下载记录保存在`log`文件里边。
```bash
nohup prefetch --option-file SraAccList > log 2>&1 &
```
###### 提升下载速度

```bash
wget https://d3gcli72yxqn2z.cloudfront.net/connect_latest/v4/bin/ibm-aspera-connect-3.11.2.63-linux-g2.12-64.tar.gz
sh ibm-aspera-connect-3.11.2.63-linux-g2.12-64.sh
```
根据Aspera Connect的官方说明，仅支持每个用户自己下载，不能放在系统上。所以只能安装在自己的用户目录下。
    The Connect installation script must not be run by user root. Connect on Linux may only be installed per user, as opposed to system wide. To avoid errors, install Connect per user, excluding user root.