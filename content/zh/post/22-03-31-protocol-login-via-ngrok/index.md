---
title: 远程登陆服务器及连接Rstudio操作指南
image:
  focal_point: "top"
---

在非校内环境下登陆计算服务器和服务器上配置的Rstudio服务的具体流程。
<!--more-->


中医药研究院目前安装有一台存储容量72T，内存32Gb的计算服务器，主要用于`Linux`环境下的数据计算和数据存储服务。目前服务器配置有生物信息学计算中常规需要使用的工作环境（blast、blat、fastQC）、FTP服务（[EBI MGnify](https://www.ebi.ac.uk/metagenomics/)标准肠道参考基因组v1和v2）和一个[ucsc genome browser](http://www.genome.ucsc.edu/)（human genome build GRCh38），后期会逐步添加计算节点以增加服务器的算力。

服务器配置的连接网络是校园网，所以无法在校园外的网络中连接和使用。为了解决在校外环境中无法访问该服务器的问题，暂时使用[ngrok](https://ngrok.com/)完成内网穿透。`ngrok`一个账户下能够使用4个`tunnel`，现在暂时开放了两个tunnel给Rstudio（port:8787）和ssh（port:22）两个功能使用。每个账户下最大可连接数是40个，应该能够满足服务器的负载和使用。

#### 如何登陆计算服务器
登陆服务器需要使用ssh服务，`Windows`系统下可以使用命令行或者下载[putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)并安装，然后使用账户登录。`Mac`系统下直接打开`terminal`即可。

`Windows`系统下命令行的使用方法根据各个系统版本略有不同，最直接的办法是在`Windows`商店安装`PowerShell`或者`Windows Terminal`。以用户名为`thor`为例，打开后运行如下命令：
```bash
ssh -l thor 0.tcp.ngrok.io -p 18703
```

回车以后会提示

#### 如何登陆Rstudio
很简单，浏览器内输入以下网页链接：
```bash
https://e032-120-85-130-202.ngrok.io 
```
该系统的账号和密码，跟服务器登陆的账号密码一致。

#### FAQ
* *在服务器操作跟自己电脑有什么区别？*

服务器直接提供了Linux工作环境，不需要在自己电脑上安装虚拟机配置。服务器直接提供了大容量的数据存储，对于一些大型计算数据来说，目前的72T存储容量基本上是没有问题的。此外，对于一些计算需求比较高的工作，在服务器上运行可以减轻自己电脑的压力；对于一些需要长时间计算的工作，可以通过`screen`或`nohup`放在服务器上自己跑，跑完看数据即可。

*  *为什么连接有点卡，甚至有时候无法登陆？*

目前使用的是`ngrok`自身提供的服务，`ngrok`的节点在美国，所以在速度上会有点慢。实际上尝试了国内的节点，速度并没有改观。登陆服务器有时候会出现无法连接的情况，具体的表现就是出现“Operation timed out”的提醒。经过几天的测试，这种无法连接的情况是暂时的，稍后再试就可以了。Rstudio的登陆比较稳定，很少出现打不开的情况。

* *如何获得服务器的账号和密码？*

请发送一份邮件到wangchao@gdpu.edu.cn邮箱或者直接wechat上告诉我


