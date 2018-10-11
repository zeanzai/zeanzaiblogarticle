---
title: "学习Centos7时的操作日志"
date: 2018-10-11T09:28:29+08:00
description: "学习Centos7时的操作日志"
keywords: "centos7 study logs"
categories:
  - "Dev"
tags:
  - "Centos7 Logs"
---

### 1 基础环境配置

- ip：192.168.100.210
- root：root1003
- 包放置地址：/opt/package
- 软件安装地址： /usr/setup

---

### 2 安装jdk1.8.0_111
- 解压<br/>
`tar zxf /opt/package/jdk-8u111-linux-x64.tar.gz -C /usr/setup`

- 设置环境变量<br/>
`vi /etc/profile` 添加以下内容：
```
export JAVA_HOME=/usr/setup/jdk1.8.0_111
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=./:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$PATH:$JAVA_HOME/bin
```

- 使环境变量生效<br/>
`source /etc/profile`

- 重启<br/>
`reboot`

---

### 3 安装Tomcat8.5

- 解压文件<br/>
`$ tar zxf apache-tomcat-8.5.32.tar.gz -C /usr/setup/`

- 创建用户，并将home目录放置到安装目录下面<br/>
`useradd -m -U -d /usr/setup/apache-tomcat-8.5.32 -s /bin/false tomcat`

- 创建快捷方式<br/>
`ln -s /usr/setup/apache-tomcat-* /usr/setup/latestTomcat`

- 改变文件所属组和用户为Tomcat<br/>
```
chown -R tomcat: /usr/setup/apache-tomcat-*
chown -R tomcat: /usr/setup/latestTomcat
```

- 可执行状态<br/>
`chmod +x /usr/setup/latestTomcat/bin/*.sh`

- 创建服务<br/>
`vi /etc/systemd/system/tomcat.service`，添加以下内容
```
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

- 让创建的服务生效，然后启动Tomcat<br/>
```
systemctl daemon-reload
systemctl start tomcat
systemctl status tomcat
```

- 加入开机自启动<br/>
`systemctl enable tomcat`

- 开放端口<br/>
```
firewall-cmd --zone=public --permanent --add-port=8080/tcp
firewall-cmd --zone=public --permanent --add-port=8005/tcp
firewall-cmd --zone=public --permanent --add-port=8009/tcp
firewall-cmd --reload
```

- 修改配置文件<br/>
	a. `vi /usr/setup/latestTomcat/conf/tomcat-users.xml`
	```添加以下内容
	<role rolename="manager-script"/>
	<user username="tomcat" password="tomcat" roles="manager-script"/>
	```
	b. `vi /usr/setup/latestTomcat/webapps/manager/META-INF/context.xml`
```
<Context antiResourceLocking="false" privileged="true" >
	<!--  <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> --> 把这一行注释掉
	<Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```

```

1. 参考： https://github.com/zeanzai/Computer-Science-Study-Note/blob/master/operation/install-tomcat8.md

2. Tomcat启动慢的解决办法
  第一种 :
  通过修改Tomcat启动文件 -Djava.security.egd=file:/dev/urandom
  再修改JRE中的java.security文件 securerandom.source=file:/dev/urandom

  第二种(推荐):
  yum -y install rng-tools
  ##启动熵服务
  systemctl start rngd
  systemctl restart rngd

```

---

### 4 安装redis4.0.6【没有设置自启动】
- 检查编译工具是否安装<br/>
`rpm -qa | grep gcc-c++`，如果没有安装，执行： `yum -y install gcc-c++`
- 解压<br/>
`tar zxf redis-4.0.6.tar.gz`
- 进入安装目录，并进行编译<br/>
`make`
- 创建安装目录<br/>
`mkdir /usr/setup/redis4.0.6_singleton`
- 安装<br/>
`make install PREFIX=/usr/setup/redis4.0.6_singleton`
- 测试<br/>
	- 进入安装目录，启动服务端<br/>
		`cd /usr/setup/redis4.0.6_singleton/bin`<br/>
		`./redis-server`
	- 开启另一个终端，进入安装目录，启动客户端<br/>
		`cd /usr/setup/redis4.0.6_singleton/bin`<br/>
		`./redis-cli`

```
一些基本命令
ping
set a 123
get a
quit
```

---

### 5 安装MySQL5.7
- 检查安装环境<br/>
```
rpm -qa|grep mariadb
rpm -e mariadb-libs-* --nodeps
rpm -qa|grep mariadb
```

- 解压<br/>
```
tar xvf mysql-5.7.16-1.el7.x86_64.rpm-bundle.tar
mkdir mysql5.7.16
mv mysql-community-* ./mysql5.7.16/
```
- 安装<br/>
```
rpm -ivh mysql-community-common-5.7.16-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.16-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.16-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-5.7.16-1.el7.x86_64.rpm
```

- 初始化<br/>
```
mysqld --initialize --user=mysql
cat /var/log/mysqld.log
systemctl status mysqld
systemctl start mysqld
systemctl status mysqld
```

- 分配权限<br/>
```
shell$ mysql -uroot -p
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root1003@study&*(';
mysql> grant all on *.* to 'root'@'%' identified by 'root1003@study&*(';
mysql> flush privileges;
```

- 开放端口<br/>
```
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
```

---

### 6 安装git
- 查看yum库中的版本<br/>
`yum info git`
- 查看是否安装<br/>
`git --version`
- 安装依赖包<br/>
`yum install -y curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker`
- 查看是否安装<br/>
`git --version`
- 卸载旧版本<br/>
`yum remove git`
- 下载 `https://github.com/git/git/releases`<br/>
- 上传到制定目录<br/>
- 解压<br/>
`tar zxf git-2.18.0.tar.gz`
- 安装<br/>
```
make prefix=/usr/setup/git_2.18.0 all
make prefix=/usr/setup/git_2.18.0 install
```
- 配置环境变量<br/>
```
vi /etc/profile
export GIT_HOME=/usr/setup/git_2.18.0
export PATH=$PATH:$GIT_HOME/bin
source /etc/profile
```
- 查看是否安装<br/>
`git --version`
- 配置<br/>
```
git config --global user.name "zeanzai"
git config --global user.email "438123371@qq.com"
```
