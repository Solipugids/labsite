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

IBM提供的`Aspera Connect`工具可以达到~10Mb/s的下载速度。在NCBI的[Download](https://www.ncbi.nlm.nih.gov/home/download/)版块上直接有`Aspera`的入口，进去后直接跳转到IBM官网下载。

可以把`Aspera Connect`压缩包直接扔到服务器用户目录下，解压后就是一个`sh`文件，执行即可。
```bash
sh ibm-aspera-connect_4.1.1.73_linux.sh
```
运行后直接在用户目录下生成一个`.aspera/connect/`的隐藏目录，例如下载`NCBI`上`NR`库`nr.gz`的调用方法就是
```bash
~/.aspera/connect/bin/ascp -i ~/.aspera/connect/etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m anonftp@ftp.ncbi.nlm.nih.gov:/blast/db/FASTA/nr.gz .
```
测试下载速度基本上可以接近`10Mb/s`左右。
根据Aspera Connect的官方说明，仅支持每个用户自己下载，不能放在系统上。所以只能安装在自己的用户目录下。
    The Connect installation script must not be run by user root. Connect on Linux may only be installed per user, as opposed to system wide. To avoid errors, install Connect per user, excluding user root.