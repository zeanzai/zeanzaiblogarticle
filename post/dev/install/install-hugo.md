---
title: "安装Hugo"
date: 2018-10-01T13:25:30+08:00
description: "在centos7上面安装hugo"
keywords: "hugo | centos | centos7 | install"
thumbnail: "img/dev/install/hugo.png"
categories:
  - "install"
tags:
  - "DevOps"
  - "Hugo"
---

## 前言

> 笔者在进行试验时，有几个操作习惯，具体[在这里](https://github.com/zeanzai/Computer-Science-Study-Note/blob/master/operation/README.md)。<br>
> 此外，Centos7默认开通了80端口和22端口。<br>
> 云服务器上面不需要执行开通端口的命令，直接在管理界面中添加安全组即可。

## 安装hugo

hugo是本教程的另一个不可或缺的工具.

具体的安装过程分为两种，一种是使用yum安装，一种是使用安装包形式安装。推荐是用yum方式安装, yum方式也是hugo的官网推荐方式.

#### 安装包安装方式

1. 查看安装包下载地址： https://github.com/gohugoio/hugo/releases/
2. 选取最新版本，并复制下载地址
3. 安装

{{< highlight shell >}}
# 1. 进入安装包存放目录
[root@izwz97ekphk0kmqgt9zvc4z ~]# cd opt/packages/

# 2. 下载安装包
[root@izwz97ekphk0kmqgt9zvc4z packages]# wget -c https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-64bit.tar.gz

# 3. 解压
[root@izwz97ekphk0kmqgt9zvc4z packages]# tar zxf hugo_0.48_Linux-64bit.tar.gz

# 4. 移动
[root@izwz97ekphk0kmqgt9zvc4z packages]# mv /usr/setup/hugo/hugo /usr/local/bin/

# 5. 测试
[root@izwz97ekphk0kmqgt9zvc4z packages]# hugo version
Hugo Static Site Generator v0.48 linux/amd64 BuildDate: 2018-08-29T06:33:51Z

{{< /highlight >}}

> 参考<br>
> https://www.jianshu.com/p/bb2d483f4caf

#### yum安装方式（建议）

1. 根据hugo官方[文档](https://gohugo.io/getting-started/installing/)提示，找到[Epel Repo](https://copr.fedorainfracloud.org/coprs/daftaupe/hugo/)下载地址，获取hugo的Epel Repo里面的[内容](https://copr.fedorainfracloud.org/coprs/daftaupe/hugo/repo/epel-7/daftaupe-hugo-epel-7.repo)
2. 安装

{{< highlight shell >}}
# 1. 将第一步获取到的内容复制到hugo.repo文件中
[root@study ~]# vi /etc/yum.repos.d/hugo.repo

# 2. 使用yum进行安装
[root@study ~]# yum -y install hugo

# 3. 测试
[root@study ~]# hugo version
Hugo Static Site Generator v0.48 linux/amd64 BuildDate: 2018-08-29T09:42:25Z
{{< /highlight >}}
