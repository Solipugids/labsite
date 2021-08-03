---
title: 挂载大容量磁盘到指定目录下
date: 2021-08-03T14:25:10.821Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
目前的磁盘容量是12T，新的磁盘需要进行格式化并作为数据存储和测试盘挂载到一个指定的目录下。
检查目前磁盘的状态，看到新的磁盘目前没有磁盘信息（Disk label type，Disk identifier）
```bash
sudo fdisk -l
```
创建分区，创建之后磁盘信息就显示出来了。
```bash
sudo parted -s ／dev／sdb mklabel gpt
sudo parted mkpart primary 2048s 100%
```
格式化
```bash
sudo mkfs.xfs -f /dev/sdb1 
```
挂载磁盘到目录`／share`下面
```bash
sudo mount /dev/sdb1 /share
```
修改`/etc/fstab`文件，保证分区可以自动挂载
```bash
/dev/sdb1 /share xfs defaults 0 0
```
查看／share目录发现目录文件已经挂载磁盘成功，容量是12T
```bash
df -h /share
```