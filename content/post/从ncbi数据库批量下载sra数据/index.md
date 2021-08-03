---
title: 从NCBI数据库批量下载SRA数据
date: 2021-08-03T14:23:37.408Z
draft: true
featured: false
authors:
  - 王超
tags: []
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
首先从NCBI下载最新版本的[NCBI SRA Toolkit](https://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=software)（链接可能会失效）。页面上提供了很多种系统下的工具下载方式，因为是安装在CentOS服务器上面的，有管理员权限，所以我选择了`Cloud - yum install script`的方式。这样安装后的SRA Toolkit可以提供给服务器上的所有用户使用。如果是在自己目录下用，可以选择`non-sudo tar archive`的方式。

###### 安装SRA Toolkit
```
wget https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.11.0/setup-yum.sh
sudo sh setup-yum.sh
```
默认安装路径是`／usr／local／ncbi`。安装完成后需要先手动添加到路径下
```
source /etc/profile.d/sra-tools.sh
```
###### 下载数据

使用一篇发表在Cancer Cell上[文章](https://doi.org/10.1016/j.ccell.2019.02.001)中附带的数据作为例子，来演示如何进行NCBI上大文件的批量下载。文章数据的索引号是SRP157974，NCBI检索发现有727条记录。
```
source /etc/profile.d/sra-tools.sh
```




