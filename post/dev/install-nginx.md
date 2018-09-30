---
title: "使用hugo创建自己的博客：（二）安装Nginx"
date: 2018-09-06T11:28:25+08:00
description: "使用hugo创建自己的博客"
thumbnail: "img/dev/nginx.jpg"
categories:
  - "dev"
tags:
  - "hugo"
---

## 前言

> 笔者在进行试验时，有几个操作习惯，具体[在这里](https://github.com/zeanzai/Computer-Science-Study-Note/blob/master/operation/README.md)。<br>
> 此外，Centos7默认开通了80端口和22端口。<br>
> 云服务器上面不需要执行开通端口的命令，直接在管理界面中添加安全组即可。

## 安装Nginx

nginx的作用在本教程中是作为web容器的. 将hugo生成的静态文件放到Nginx里面, 就可以通过访问Nginx来访问博客了.

{{< highlight shell >}}
# 1. 创建安装目录
[root@izwz97ekphk0kmqgt9zvc4z ~]# mkdir /usr/setup/nginx_1.12.2
[root@izwz97ekphk0kmqgt9zvc4z ~]# mkdir /usr/setup/nginx_1.12.2/temp

# 2. 安装依赖
[root@izwz97ekphk0kmqgt9zvc4z ~]# yum install -y gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel

# 3. 下载安装包并上传到/opt/package

# 4. 解压
[root@izwz97ekphk0kmqgt9zvc4z ~]# cd /opt/packages/
[root@izwz97ekphk0kmqgt9zvc4z packages]# tar zxf nginx-1.12.2.tar.gz
[root@izwz97ekphk0kmqgt9zvc4z packages]# cd nginx-1.12.2/

# 5. 配置
[root@izwz97ekphk0kmqgt9zvc4z nginx-1.12.2]# ./configure --prefix=/usr/setup/nginx_1.12.2 \
> --pid-path=/usr/setup/nginx_1.12.2/nginx.pid \
> --lock-path=/usr/setup/nginx_1.12.2/lock/nginx.lock \
> --error-log-path=/usr/setup/nginx_1.12.2/log/error.log \
> --http-log-path=/usr/setup/nginx_1.12.2/log/access.log \
> --http-client-body-temp-path=/usr/setup/nginx_1.12.2/temp/client \
> --http-proxy-temp-path=/usr/setup/nginx_1.12.2/temp/proxy \
> --http-fastcgi-temp-path=/usr/setup/nginx_1.12.2/temp/fastcgi \
> --http-uwsgi-temp-path=/usr/setup/nginx_1.12.2/temp/uwsgi \
> --http-scgi-temp-path=/usr/setup/nginx_1.12.2/temp/scgi \
> --with-http_stub_status_module \
> --with-http_gzip_static_module \
> --with-http_ssl_module

[root@izwz97ekphk0kmqgt9zvc4z nginx-1.12.2]# make
[root@izwz97ekphk0kmqgt9zvc4z nginx-1.12.2]# make install

# 6. 开放80端口，并进行测试
[root@izwz97ekphk0kmqgt9zvc4z nginx_1.12.2]# /usr/setup/nginx_1.12.2/sbin/nginx

# 7. 配置服务文件
[root@izwz97ekphk0kmqgt9zvc4z nginx_1.12.2]# cd /etc/systemd/system
[root@izwz97ekphk0kmqgt9zvc4z system]# vi nginx.service
  [Unit]
  Description=nginx
  After=network.target

  [Service]
  Type=forking
  ExecStart=/usr/setup/nginx_1.12.2/sbin/nginx
  ExecReload=/usr/setup/nginx_1.12.2/sbin/nginx -s reload
  ExecStop=/usr/setup/nginx_1.12.2/sbin/nginx -s quit
  PrivateTmp=true

  [Install]
  WantedBy=multi-user.target

[root@izwz97ekphk0kmqgt9zvc4z system]# systemctl enable nginx.service
[root@izwz97ekphk0kmqgt9zvc4z system]# systemctl start nginx.service

# 8. 启动Nginx，并进行测试

{{< /highlight >}}

**扩展**：

- 启动和关闭

```shell
// 切换到Nginx安装目录
$ cd /usr/setup/nginx_1.12.2/sbin

// 启动 （也可以加上 -C /usr/setup/nginx_1.12.2/config/nginx.conf 配置文件）
$ ./nginx

// 关闭
第一种方式 查找相关进程，然后kill进程
第二种方式 ./nginx -s stop
第三种方式 ./nginx -s quit
```

- 关于centos7的系统服务命令

```shell
$ systemctl start nginx.service
$ systemctl enable nginx.service
$ systemctl disable nginx.service
$ systemctl status nginx.service
$ systemctl restart nginx.service
```
