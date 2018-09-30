---
title: "使用hugo创建自己的博客：（四）安装Git"
date: 2016-09-06T18:24:05+08:00
description: "使用hugo创建自己的博客"
thumbnail: "img/dev/git.jpg"
categories:
  - "dev"
tags:
  - "hugo"
---



## 前言

> 笔者在进行试验时，有几个操作习惯，具体[在这里](https://github.com/zeanzai/Computer-Science-Study-Note/blob/master/operation/README.md)。<br>
> 此外，Centos7默认开通了80端口和22端口。<br>
> 云服务器上面不需要执行开通端口的命令，直接在管理界面中添加安全组即可。


## 安装git

Git作为服务器与GitHub沟通(当然这种说法不够严谨, 应该说是博客的版本控制)的关键工具必不可少, 当然也需要安装了.

依然采用源码的安装方式进行安装.

基本过程如下:

- 删除旧版本
- 安装依赖
- 下载源码
- 解压
- 执行安装过程
- 配置

完整过程如下:


{{< highlight shell >}}
# 1. 查看是否已经安装
[root@iZwz97ekphk0kmqgt9zvc4Z ~]# git --version

# 2. 安装依赖包
[root@iZwz97ekphk0kmqgt9zvc4Z ~]# yum install -y curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker

# 3. 进入安装包存放目录
[root@iZwz97ekphk0kmqgt9zvc4Z ~]# cd /opt/packages/

# 4. 下载
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# wget -c https://github.com/git/git/archive/v2.18.0.tar.gz

# 5. 解压到当前目录
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# tar zxf v2.18.0.tar.gz

# 6. 创建安装目录
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# mkdir /usr/setup/git_2.18.0

# 7. 进入源码目录，并进行make配置
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# cd git-2.18.0/
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# make prefix=/usr/setup/git_2.18.0 all
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# make prefix=/usr/setup/git_2.18.0 install

# 8. 配置环境变量，并使配置生效
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# vi /etc/profile
# 添加以下内容
export GIT_HOME=/usr/setup/git_2.18.0
export PATH=$PATH:$GIT_HOME/bin
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# source /etc/profile

# 9. 测试
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# git --version

# 10. 配置git全局用户名和邮箱
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# git config --global user.name "zeanzai"
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# git config --global user.email "438123371@qq.com"
{{< /highlight >}}
