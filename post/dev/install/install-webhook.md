---
title: "安装Webhook"
date: 2018-10-01T12:27:14+08:00
description: "在CentOS7上安装Webhook"
keywords: "webhook | centos | centos7 | install"
thumbnail: "img/dev/install/webhook.jpg"
categories:
  - "install"
tags:
  - "DevOps"
  - "Webhook"
---

## 前言

> 笔者在进行试验时，有几个操作习惯，具体[在这里](https://github.com/zeanzai/Computer-Science-Study-Note/blob/master/operation/README.md)。<br>
> 此外，Centos7默认开通了80端口和22端口。<br>
> 云服务器上面不需要执行开通端口的命令，直接在管理界面中添加安全组即可。

## 安装webhook

webhook可以理解成可以通过远程调用来执行的相关命令的工具。

举个例子来说明：

> 需求分析: <br >
>   远程调用一个命令后，服务器打印当前服务器的时间到`/opt/scripts/hugo-deploy/redeploy.log`


{{< highlight shell >}}
# 1. 安装webhook客户端
[root@izwz97ekphk0kmqgt9zvc4z ~]# go get github.com/adnanh/webhook
# 这一步需要安装golang，执行webhook安装过程后，webhook的相关文件就在安装golang时配置的gopath目录下面

# 2. 创建可执行的文件
[root@izwz97ekphk0kmqgt9zvc4z ~]# mkdir -p /opt/scripts/hugo-deploy
[root@izwz97ekphk0kmqgt9zvc4z ~]# vi /opt/scripts/hugo-deploy/redeploy.sh
  #!/bin/bash

  time=`date "+%Y-%m-%d %H:%M:%S"`
  echo "${time}" >> /opt/scripts/hugo-deploy/redeploy.log
[root@izwz97ekphk0kmqgt9zvc4z ~]# chmod +x /opt/scripts/hugo-deploy/redeploy.sh

# 3. 测试是否能够打印
[root@izwz97ekphk0kmqgt9zvc4z ~]# /opt/scripts/hugo-deploy/redeploy.sh
[root@izwz97ekphk0kmqgt9zvc4z ~]# cat /opt/scripts/hugo-deploy/redeploy.log

# 4. 创建hooks.json文件
[root@izwz97ekphk0kmqgt9zvc4z ~]# vi /opt/scripts/hugo-deploy/hooks.json
  [
    {
      "id": "redeploy-webhook",
      "execute-command": "/opt/scripts/hugo-deploy/redeploy.sh",
      "command-working-directory": "./"
    }
  ]

# 5. 启动webhook客户端
[root@izwz97ekphk0kmqgt9zvc4z ~]# /usr/setup/go/gopath/bin/webhook -hooks /opt/scripts/hugo-deploy/hooks.json -port=9876 -verbose

# 6. 开放9876端口，并测试
在阿里云安全组上放开9876端口，然后在浏览器中输入：http://<your-ip>:9876/hooks/redeploy-webhook
打印log文件

# 7. 创建服务
[root@izwz97ekphk0kmqgt9zvc4z ~]# cd /etc/systemd/system
[root@izwz97ekphk0kmqgt9zvc4z system]# vi webhook.service
  [Unit]
  Description=Webhooks

  [Service]
  ExecStart=/usr/setup/go/gopath/bin/webhook -hooks /opt/scripts/hugo-deploy/hooks.json -port=9876 -hotreload

  [Install]
  WantedBy=multi-user.target
[root@izwz97ekphk0kmqgt9zvc4z system]# systemctl enable webhook.service
[root@izwz97ekphk0kmqgt9zvc4z system]# systemctl start webhook.service

# 8. 测试

{{< /highlight >}}


> 参考<br>
> https://davidauthier.wearemd.com/blog/deploy-using-github-webhooks.html
