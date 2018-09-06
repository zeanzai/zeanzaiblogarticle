---
title: "安装golang"
date: 2018-09-06T18:12:55+08:00
description: "hugo开发"
thumbnail: "img/dev/golang.jpg"
categories:
  - "dev"
tags: ["hugo"]
---

## 前言

> 笔者在进行试验时，有几个操作习惯，具体[在这里](https://github.com/zeanzai/Computer-Science-Study-Note/blob/master/operation/README.md)。<br>
> 此外，Centos7默认开通了80端口和22端口。<br>
> 云服务器上面不需要执行开通端口的命令，直接在管理界面中添加安全组即可。

## 安装golang

golang是go编程语言编译运行的基础环境，也是go语言开发的应用程序的运行环境。

由于后期需要把博客发布到自己的云服务器上去，并实现自动化运维（即，将博客文章发布到GitHub上，然后使用webhook工具给云服务器发送一条命令，使云服务器自动从GitHub上面pull文章，然后执行hugo命令生成网站）。

笔者使用开源项目[adnanh webhook](https://github.com/adnanh/webhook), adnanh webhook的开发语言就是Go语言, 所以需要安装golang作为基础开发环境.

#### 安装过程

采用源码安装模式的原因是因为国内的golang的官网访问很慢.

{{< highlight shell >}}
# 1. 进入安装包存放位置
[root@iZwz97ekphk0kmqgt9zvc4Z ~]# cd /opt/packages/

# 2. 下载
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# wget -c https://studygolang.com/dl/golang/go1.11.linux-amd64.tar.gz
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# ll
total 124188
-rw-r--r-- 1 root root 127163815 Aug 25 06:10 go1.11.linux-amd64.tar.gz

# 3. 创建安装目录
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# mkdir /usr/setup

# 4. 解压到安装目录
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# tar zxf go1.11.linux-amd64.tar.gz -C /usr/setup/
[root@iZwz97ekphk0kmqgt9zvc4Z packages]# cd /usr/setup/
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# ll
total 4
drwxr-xr-x 10 root root 4096 Aug 25 04:41 go

# 5. 创建gopath目录结构
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# mkdir go/gopath/
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# mkdir go/gopath/bin
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# mkdir go/gopath/src
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# mkdir go/gopath/pkg

# 6. 配置环境变量
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# vi /etc/profile
# 添加以下内容
export GOROOT=/usr/setup/go
export GOPATH=/usr/setup/go/gopath
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin

# 7. 使配置文件生效
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# source /etc/profile

# 8. 测试
[root@iZwz97ekphk0kmqgt9zvc4Z setup]# go version
go version go1.11 linux/amd64

{{< /highlight >}}

> 参考:<br>
> https://www.cnblogs.com/chy123/p/6750347.html<br>
> https://studygolang.com/dl<br>
> https://blog.csdn.net/shida_csdn/article/details/79441694<br>
