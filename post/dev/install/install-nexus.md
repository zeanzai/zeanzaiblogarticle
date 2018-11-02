---
title: "在Windows上安装Nexus，并使用nexus上传jar包"
date: 2018-11-01T10:36:24+08:00
description: "在Windows上面安装nexus的试验"
keywords: "nexus install windows"
categories:
  - "install"
tags:
  - "DevOps"
  - "Nexus"
---

## 1. 私服简介
私服是架设在局域网的一种特殊的远程仓库，目的是代理远程仓库及部署第三方构件。有了私服之后，当 Maven 需要下载构件时，直接请求私服，私服上存在则下载到本地仓库；否则，私服请求外部的远程仓库，将构件下载到私服，再提供给本地仓库下载。

<center><img title="无私服的拓扑结构" src="/img/dev/install/no-nexus.png"></center>
<center><img title="有私服的拓扑结构" src="/img/dev/install/has-nexus.png"></center>

我们可以使用专门的 Maven 仓库管理软件来搭建私服，比如：Apache Archiva，Artifactory，Sonatype Nexus。这里我们使用 Sonatype Nexus。

## 2. nexus简介与安装

### 2.1 简介
Nexus 专业版是需要付费的.

### 2.2 下载
因为试验平台为Windows平台，所以下载`nexus-latest-bundle.zip`，<a href="https://help.sonatype.com/repomanager2/download" target="_blank">下载地址在这</a>。
<img src="/img/dev/install/download.png">

### 2.3 解压
<img src="/img/dev/install/unzip.png">

### 2.4 配置环境变量
<img src="/img/dev/install/path.png">

### 2.5 使用管理员角色打开cmd，执行命令
**特别提醒使用管理员角色打开cmd**，不然在执行install命令时，会报没有权限错误。
<img src="/img/dev/install/cmd.png">

### 2.6 打开服务
<img src="/img/dev/install/start-service.png">

### 2.7 测试
<img src="/img/dev/install/test.png">
到此说明nexus安装成功。

## 3. nexus中的概念
点击右上角 Log In，使用用户名：admin ，密码：admin123 登录，可使用更多功能.

### 3.1 预置仓库类型
登陆Nexus，在左边菜单栏里选择Repositories，然后会出现右边的画面，右边上半部分是列出来的repository，黑体字是类型为group的repository。
这里简单介绍下几种repository的类型：

- hosted，本地仓库，通常我们会部署自己的构件到这一类型的仓库。比如公司的第二方库。
- proxy，代理仓库，它们被用来代理远程的公共仓库，如maven中央仓库。
- group，仓库组，用来合并多个hosted/proxy仓库，当你的项目希望在多个repository使用资源时就不需要多次引用了，只需要引用一个group即可。

<img src="/img/dev/install/nexus-repo-type.png">
<img src="/img/dev/install/nexus-group.png">

### 3.2 三种本地仓库
我们前面讲到类型为hosted的为本地仓库，Nexus预置了3个本地仓库，分别是Releases, Snapshots, 3rd Party. 分别讲一下这三个预置的仓库都是做什么用的:

- Releases:<br />
这里存放我们自己项目中发布的构建, 通常是Release版本的, 比如我们自己做了一个FTP Server的项目, 生成的构件为ftpserver.war, 我们就可以把这个构建发布到Nexus的Releases本地仓库. 关于符合发布后面会有介绍.
- Snapshots:<br />
这个仓库非常的有用, 它的目的是让我们可以发布那些非release版本, 非稳定版本, 比如我们在trunk下开发一个项目,在正式release之前你可能需要临时发布一个版本给你的同伴使用, 因为你的同伴正在依赖你的模块开发, 那么这个时候我们就可以发布Snapshot版本到这个仓库, 你的同伴就可以通过简单的命令来获取和使用这个临时版本.
- 3rd Party:<br />
顾名思义, 第三方库, 你可能会问不是有中央仓库来管理第三方库嘛,没错, 这里的是指可以让你添加自己的第三方库, 比如有些构件在中央仓库是不存在的. 比如你在中央仓库找不到Oracle 的JDBC驱动, 这个时候我们就需要自己添加到3rdparty仓库。

## 4. 使用nexus

### 4.1 覆盖中央仓库的默认地址
修改maven中的配置文件setting.xml
```
<mirror>
  <id>nexusMirror</id>
  <mirrorOf>nexus, central</mirrorOf>
  <name>Human Readable Name for this Mirror.</name>
  <url>http://127.0.0.1:8081/nexus/content/groups/public/</url>
</mirror>
```

### 4.2 配置认证
1. nexus添加用户

2. setting.xml中配置用户

3. setting.xml中配置仓库

### 4.3 将jar上传到私服
创建仓库
创建仓库组
上传（使用插件上传、直接上传）
查看上传结果

### 4.4
使用私服上的jar

## 5. 参考

https://www.cnblogs.com/tyhj-zxp/p/7605879.html
