---
title: "在centos7上面搭建Jenkins自动化部署平台"
date: 2018-11-12T09:18:51+08:00
description: "在centos7上面搭建Jenkins自动化部署平台"
keywords: "maven tomcat jenkins svn jdk"
categories:
  - "dev"
tags:
  - "jenkins"
---

# 1. 概述
## 1.1 前言
在互联网企业中，运维人员的职位不可获取，他们的主要工作是完成日常文件的备份、服务器状态的监控与日志。不同的企业之间，对运维人员的工作内容不尽相同，但是对于运维的思路却是有共通之处的。早期的运维人员大多数是通过手工方式执行的，但繁杂冗余的工作内容对于手工进行的运维方式无疑是一种人力资源的浪费。因此，**自动化运维也就成了运维人员的必备本领**。
自动化运维出现之前的三个阶段：

1. 纯手工阶段： 此阶段运维人员通过手工方式，重复的进行软件的部署和运维。
2. 脚本阶段： 此阶段，运维人员已经开始写一些脚本文件，通过执行脚本文件来完成软件部署和运维。
3. 工具阶段： 此阶段，运维人员开始借助第三方工具，高效方便的完成运维工作。

对于软件开发人员来说，掌握部分的运维（主要指软件部署、运营、维护、服务器监控和调优等）工作不仅能够让比运维人员更加熟悉软件系统的软件开发人员更好的定位问题、排查问题，也能够利用软件开发人员的专业技能，对服务器进行资源的监控与调优，所以，**必备的运维知识对于软件开发人员也日益成为一种刚需**。

在传统的软件开发过程中，软件人员开发完系统后，往往会先将代码提交到版本控制服务器，然后通过构建工具对代码执行构建过程，最后将构建的结果提交到服务器进行发布部署。但对于测试环境来说，很多时候只更改了部分的项目代码，为了立即查看到修改后的结果，就需要立即部署到测试服务器查看效果（这个过程我们称为**持续集成**）。这个过程每提交一个BUG修复，就需要执行一遍。这对于软件开发人员来说无疑是重复的工作。**为了提高这个过程（持续集成）的效率，我们需要借助自动化工具，这也是我们的这篇文章的主要内容。**

## 1.2 自动化工具
自动化工具有很多，有些是针对特定应用场景的、有些是针对特定语言的、也有些是针对特定系统平台的。这里我们着重介绍两款自动化工具。

1. **Ansible**<br />
  Ansible 是一个模型驱动的配置管理器，支持多节点发布、远程任务执行。默认使用 SSH 进行远程连接。无需在被管理节点上安装附加软件，可使用各种编程语言进行扩展。它是由Python语言编写而成。大多也使用于Python的系统部署场景中。
2. **Jenkins**<br />
  Jenkins是一款由Java编写的开源的持续集成工具。它提供了软件开发的持续集成服务，主要运行在Servlet容器中（如：Apache Tomcat），支持软件配置管理工具（如Git、CVS、Subversion等），同时可以执行基于Apache Ant和Apache Maven的项目，以及任意的Shell执行脚本和Windows批处理命令。此外，它也是基于MIT许可证下发布的自由软件。

这里，我们通过使用Jenkins自动化部署java项目代码来介绍Jenkins完成持续集成的过程。

---

# 2. 安装jdk
上一章提到Jenkins主要运行到Servlet容器中，这里为了运行Jenkins，我们使用Tomcat，而Tomcat又依赖jdk，因此需要先安装jdk。当然，我们也可以通过Docker容器的方式进行启动Jenkins，或者使用jar命令直接运行Jenkins的war包，这里我们使用tomcat容器方式启动。要想使用tomcat，就必须安装jdk，因为tomcat依赖jdk。故，jdk的安装步骤如下：

1. 创建`安装包存放目录`和`自定义软件安装目录`
  ```
  [root@JenkinsServer ~]# mkdir /opt/package
  [root@JenkinsServer ~]# mkdir /usr/setup
  ```

2. 下载jdk，并上传到第一步创建的`安装包存放目录`
  ```
  略。
  ```

3. 将jdk安装包解压到`自定义软件安装目录`
  ```
  [root@JenkinsServer ~]# tar zxf /opt/package/jdk-8u111-linux-x64.tar.gz -C /usr/setup
  ```

4. 设置环境变量
  ```
  [root@JenkinsServer ~]# vi /etc/profile
  # 添加以下内容
  export JAVA_HOME=/usr/setup/jdk1.8.0_111
  export JRE_HOME=$JAVA_HOME/jre
  export CLASSPATH=./:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
  export PATH=$PATH:$JAVA_HOME/bin
  ```

5. 使环境变量生效
  ```
  [root@JenkinsServer ~]# source /etc/profile
  ```

6. 测试
  ```
  [root@JenkinsServer ~]# java -version
  [root@JenkinsServer ~]# java
  [root@JenkinsServer ~]# javac
  ```

---

# 3. 安装tomcat
安装完jdk之后，我们就要开始tomcat8.5的安装了。这里我们是使用tomcat8.5的版本，下面是安装步骤：

1. 上传tomcat安装包到`安装包存放目录`
  ```
  略
  ```

2. 将安装包解压到`自定义软件安装目录`
  ```
  [root@JenkinsServer ~]# tar zxf /opt/package/apache-tomcat-8.5.32.tar.gz -C /usr/setup/
  ```

3. 创建用户，并将home目录放置到安装目录下面
  ```
  [root@JenkinsServer ~]# useradd -m -U -d /usr/setup/apache-tomcat-8.5.32 -s /bin/false tomcat
  ```

4. 创建快捷方式
  ```
  [root@JenkinsServer ~]# ln -s /usr/setup/apache-tomcat-* /usr/setup/latestTomcat
  ```

5. 改变文件所属组和用户为Tomcat
  ```
  [root@JenkinsServer ~]# chown -R tomcat: /usr/setup/apache-tomcat-*
  [root@JenkinsServer ~]# chown -R tomcat: /usr/setup/latestTomcat
  ```

6. 赋予可执行权限
  ```
  [root@JenkinsServer ~]# chmod +x /usr/setup/latestTomcat/bin/*.sh
  ```

7. 创建`/etc/systemd/system/tomcat.service`文件
  ```
  [root@JenkinsServer ~]# vi /etc/systemd/system/tomcat.service
  # 添加以下内容
  [Unit]
  Description=Tomcat 8.5 servlet container
  After=network.target
  [Service]
  Type=forking
  User=tomcat
  Group=tomcat
  Environment="JAVA_HOME=/usr/setup/jdk1.8.0_111"
  Environment="CATALINA_BASE=/usr/setup/latestTomcat"
  Environment="CATALINA_HOME=/usr/setup/latestTomcat"
  Environment="CATALINA_PID=/usr/setup/latestTomcat/temp/tomcat.pid"
  ExecStart=/usr/setup/latestTomcat/bin/startup.sh
  ExecStop=/usr/setup/latestTomcat/bin/shutdown.sh
  [Install]
  WantedBy=multi-user.target
  ```

8. 让创建的服务生效，然后启动Tomcat
  ```
  [root@JenkinsServer ~]# systemctl daemon-reload
  [root@JenkinsServer ~]# systemctl start tomcat
  [root@JenkinsServer ~]# systemctl status tomcat
  ```

9. 加入开机自启
  ```
  [root@JenkinsServer ~]# systemctl enable tomcat
  ```

10. 开放端口
  ```
  [root@JenkinsServer ~]# firewall-cmd --zone=public --permanent --add-port=8080/tcp
  [root@JenkinsServer ~]# firewall-cmd --zone=public --permanent --add-port=8005/tcp
  [root@JenkinsServer ~]# firewall-cmd --zone=public --permanent --add-port=8009/tcp
  [root@JenkinsServer ~]# firewall-cmd --reload
  ```

11. 修改配置文件
  ```
  [root@JenkinsServer ~]# vi /usr/setup/latestTomcat/conf/tomcat-users.xml
  # 添加以下内容
<role rolename="manager-script"/>
<user username="tomcat" password="tomcat" roles="manager-script"/>
  ```
  ```
  [root@JenkinsServer ~]# vi /usr/setup/latestTomcat/webapps/manager/META-INF/context.xml
  <Context antiResourceLocking="false" privileged="true" >
<!--  <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> --> 把这一行注释掉
<Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
  ```

12. 测试
  ```
  在浏览器输入localhost:8080，查看效果。
  ```

---

# 4. 安装maven
此外，由于公司开发的大大小小的项目都是maven项目，所以maven也是必不可少的。

1. 上传到maven安装包到`安装包存放目录`
  ```
  略
  ```

2. 将安装包解压到`自定义软件安装目录`
  ```
  [root@JenkinsServer ~]# tar zxf /opt/package/apache-maven-3.5.0-bin.tar.gz -C /usr/setup/
  ```

3. 设置环境变量
  ```
  [root@JenkinsServer ~]# vi /etc/profile
  # 添加以下内容
  export export MAVEN_HOME=/usr/setup/apache-maven-3.5.0
  # 并修改下面内容
  export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin
  ```

4. 使环境变量生效
  ```
  [root@JenkinsServer ~]# source /etc/profile
  ```

5. 测试
  ```
  [root@JenkinsServer ~]# mvn -v
  ```

6. 配置仓库地址
  ```
  # 创建仓库文件夹
  [root@JenkinsServer ~]# mkdir /opt/repository
  # 修改仓库settings.xml配置文件
  [root@JenkinsServer ~]# vi /usr/setup/apache-maven-3.5.0/conf/settings.xml
  修改为： <localRepository>/opt/repository</localRepository>
  ```

---

# 5. 安装Jenkins并完成初始化
## 5.1 安装Jenkins
最后一步便是安装Jenkins了。此步骤安装过程非常简单，只需要下载Jenkins的war包（版本为：Jenkins 2.138.3），将其放入tomcat的webapp目录下面，最后启动tomcat即可。

## 5.2 初始化配置
在浏览器中输入`localhost:8080/jenkins`进入操作界面。
<img src="/img/dev/jenkins-server/001.png" target="_blank"/>
根据要求找到密码，并复制到密码框中。
```
[root@JenkinsServer ~]# cat /usr/setup/apache-tomcat-8.5.32/.jenkins/secrets/initialAdminPassword
```

跳过插件安装。
<img src="/img/dev/jenkins-server/002.png" title="跳过插件安装" style="border: solid 1px red;" />

不创建管理员用户，使用默认管理员用户登录。
<img src="/img/dev/jenkins-server/003.png" title="使用admin继续" style="border: solid 1px red;" />

使用默认实例配置URL
<img src="/img/dev/jenkins-server/004.png" title="配置默认url" style="border: solid 1px red;" />

到此结束后，整个初始化配置完成。

初始化完成之后，需要修改admin密码。可以通过登录的操作界面找到admin用户，然后更新密码即可。

---

# 6. Jenkins的配置

初始化完成后，需要对Jenkins的操作界面有所了解，因为以后对项目的所有操作都将会在这个界面中完成。Jenkins的操作界面主要分为两大部分，一左一右，左边相当于我们常用的菜单，右边相当于每一个菜单下面的子菜单。功能正如第一章节所说的那样，包括任务管理、构建管理以及Jenkins的系统管理等。
对Jenkins的操作界面的熟悉后，下一个步骤就是集成jdk和maven以及安装必要的插件了。
## 6.1 集成jdk和maven
点击右边菜单的“系统管理”，然后选择“全局工具配置”，找到前几步安装的jdk和maven的根目录，拷贝到相应位置保存即可。
<img src="/img/dev/jenkins-server/global-config.png" title="全局工具配置" style="border: solid 1px red;" />

## 6.2 安装插件
由于需要部署的项目是maven项目，并且项目源码位于另外一台svn服务器上面，所以需要两个插件——`maven integration`和`Subversion`，此外，项目源码从svn上面下载到Jenkins上面的工作空间后，需要进行编译、打包，最后发布到tomcat容器中去，所以还需要`Deploy to container`。具体过程，点击右边菜单的“系统管理”，选择“插件管理”，在“可选插件列表中”找到三个插件`maven integration`、`Subversion`、`Deploy to container`，并打上勾，最后点击最下面的“直接安装”即可。安装完成后，在“已安装”标签页中找到刚才安装的三个插件。如果在安装插件时，遇到问题，可以通过检查以下几个问题进行排除：

1. 服务器网络是否能够ping通；（我的服务器是静态ip，安装时未配置DNS）
2. 通过点击Jenkins右边的“系统管理”，选择系统日志，查看日志内容，然后进行问题的定位


**题外话：针对项目的类型、项目所使用的版本库以及项目构建的过程的不同，所需要的插件也不尽相同。**

---

# 7. 创建第一个项目
点击首页的“创建一个新任务”按钮。填入任务名称，选择项目类型（构建一个maven项目），点击确定，进入配置界面：
<img src="/img/dev/jenkins-server/config.png" title="任务配置" style="border: solid 1px red;" />

解释：

1. General：此框内的内容主要是针对上一步创建的任务的描述以及构建保留的时间等的设置；
2. 源码管理：默认是无，如果上面过程中已经安装过svn的插件，这地方就会显示svn的选项，同理，如果是git仓库，那需要安装git的插件，之后才会显示git的操作界面；
3. 构建触发器：此处配置任务创建完成后的构建操作的触发器，即通过什么事件触发构建过程。这里常用的有三种方式，一种是轮询版本库，一旦版本库中发生提交或者修改操作，就会触发构建过程；一种是定时器方式，即根据时钟定时触发构建过程；第三种默认方式，即通过手动方式进行构建。
4. Pre Steps：即在执行构建过程之前需要Jenkins做的事情。
5. Build：即使用哪个文件构建，构建过程的运行命令等。
6. Post Steps：即执行完构建过程之后需要Jenkins做的事情。

---

# 8. 构建
上一步骤中，配置了一个Jenkins项目的构建过程，但是并未执行，这里只需要对配置好的构建过程执行构建操作即可。
## 8.1 手动构建
完成上一步对构建任务的配置之后，就会在Jenkins右边看板中出现任务列表，单击任务的构建图标（即每一行的最后一个图标）即可完成手动构建过程。

## 8.2 自动构建
自动构建过程需要在对任务配置相应的触发器，配置触发器可以通过编辑任务配置来完成。即在看板中的任务列表，鼠标放置到任务名称上，会出现一个倒三角，点击倒三角中的配置按钮，进入配置界面，然后填写构建触发器信息，点击完成即可。

---

# 9. 查看日志
Jenkins系统中的日志分为两大类，**一类是系统日志，一类是构建日志**。系统日志主要是记录系统启动、系统读取配置文件、系统安装插件等全局式的日志；而构建日志，则是每一个构建任务都会有一个构建日志，构建日志中则主要记录了执行整个构建过程的日志。通常定位系统中的问题时，我们是通过查看日系统日志来排查的，例如：安装插件的问题排查等；在定位某一个构建过程中出现的问题时，我们则是通过构建日志来完成的，例如：查看某一个构建过程是否执行成功。

---

# 10. 结语
此外，Jenkins中还可以配置邮件通知系统，即执行构建过程结束后给自定义邮箱发送邮件的功能；还有配置管理员用户等的权限管理等，此处，我们不再介绍这些功能，有兴趣的童鞋可以自行搜索配置。

总结一下，本文主要介绍了从零开始使用Jenkins系统构建一个机遇maven的javaWeb项目，过程依次是：`安装jdk、tomcat、maven、Jenkins`、`初始化Jenkins`、`配置Jenkins`、`构建第一个任务`……其中也穿插介绍了一下，在完成整个构建过程的配置以后，如何排查在构建过程中所遇到的问题的方式——查看日志等。总的来说，不管`gradle`风格项目还是`maven`风格项目，构建过程都是大同小异，构建过程同样都包括通过版本库下载源代码、编译源代码、构建前操作、构建、构建后操作等几个过程，此外最重要的便是配置构建的触发器。掌握此构建的基本思路（或叫套路），基本已经能够完全掌握了Jenkins的所有操作了。

---

**参考资料**

1. Jenkins（软件）wiki：https://zh.wikipedia.org/wiki/Jenkins_(%E8%BD%AF%E4%BB%B6)
2. 开源的自动化部署工具探索：https://my.oschina.net/u/1540325/blog/639884
3. 这21个自动化部署工具，你都知道吗？：https://blog.csdn.net/qq_25711251/article/details/72869682
4. Jenkins 持续集成综合实战：https://blog.csdn.net/kefengwang/article/details/54233584
5. 使用Jenkins持续集成前端项目并自动化部署到Nginx服务器： https://segmentfault.com/a/1190000013481128
6. 使用jenkins实现tomcat自动化部署： http://blog.51cto.com/ellenv/1932817
7. jenkins+maven+svn 自动化部署：https://www.cnblogs.com/arnoLixi/p/9241847.html
