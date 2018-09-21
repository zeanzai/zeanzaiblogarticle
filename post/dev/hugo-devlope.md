---
title: "使用hugo创建自己的博客"
date: 2018-09-06T09:12:55+08:00
description: "hugo开发"
thumbnail: "img/domain.jpg"
categories:
  - "dev"
tags: ["hugo"]
---

## 整个开发流程

在此, 笔者定义了一个完整的开发流程, 使得在不同的平台上面都能完成只需要发布文章然后提交到 GitHub 就能够自动化部署的目标.

笔者将以不同场景的操作方式来讲述这个过程.

1. 公司
  在公司的电脑上, 安装好hugo, 然后在特定目录下面生成博客的根目录, 使用Atom写文档, 写完之后使用Hugo命令查看一下在本机上的效果.
  写完文档之后, 使用sourcetree上传到GitHub上面去, 上传完成后, GitHub会调用钩子, 个人的阿里云服务器会执行pull操作, 然后重新部署, 达到更新的目的.
2. 家里
  同样的开发环境和开发软件, 同样的开发过程, 达到持续部署的目的.


该步骤为关键步骤, 主要分为`分解文档结构`, `上传文档到各自仓库`, `克隆项目到服务器`, `创建webhook命令`, `在GitHub上面配置webhook`, `测试`几个步骤.


#### 分解文档结构

将hugo初始化的根目录分解, 分解成四个目录, 这样分解的目的是为了区分不同仓库的功能, 也是为了更好的维护.

```
hugo
  |- <username>.github.io
  |- <username>blogarticle
  |- <username>blogtheme
  |- <username>blogbase
```

讲解:
- `<username>.github.io` 为使用GitHubPage所创建的项目, 保存的是执行 hugo 命令后生成的静态文件, 相当于根目录中的 public 文件夹.
- `<username>blogarticle` 文章目录, 保存的是所有的 md 文档, 相当于根目录中的 content 文件夹.
- `<username>blogtheme` 主题目录, 保存的是自定义的主题, 相当于根目录中的 theme 文件夹.
- `<username>blogbase` 其他目录, 保存的是除上面三个目录外的其他目录.

#### 上传文档到各自仓库
在开发用的机子上面创建以上文件夹，并完成hugo的配置，然后将文件上传到github上面，总共是四个仓库。

#### 配置服务器
需要安装一下软件:

1. [安装Nginx](/post/dev/install-nginx)
2. [安装Webhook](/post/dev/install-webhook)
3. [安装hugo](/post/dev/install-hugo)

#### 克隆项目到服务器
1. 生成服务器的秘钥

```shell
[root@izwz97ekphk0kmqgt9zvc4z ~]# ssh-keygen -t rsa -C "438123371@qq.com"
```
2. 将/root/.ssh/id_rsa.pub文件中的字符串复制到GitHub添加sshkey的界面中

3. 创建仓库存放目录，并进入

```
[root@izwz97ekphk0kmqgt9zvc4z ~]# mkdir -p /home/project/hugoblog
[root@izwz97ekphk0kmqgt9zvc4z ~]# cd /home/project/hugoblog/
```

4. 克隆

```
[root@izwz97ekphk0kmqgt9zvc4z hugoblog]# git clone https://github.com/zeanzai/zeanzaiblogbase.git
[root@izwz97ekphk0kmqgt9zvc4z hugoblog]# git clone git@github.com:zeanzai/zeanzai.github.io.git
[root@izwz97ekphk0kmqgt9zvc4z hugoblog]# git clone https://github.com/zeanzai/zeanzaiblogtheme.git
[root@izwz97ekphk0kmqgt9zvc4z hugoblog]# git clone https://github.com/zeanzai/zeanzaiblogarticle.git
```

#### 创建webhook命令

#### 在GitHub上面配置webhook

#### 测试

1.
2.

6. 配置Nginx

```shell
[root@izwz97ekphk0kmqgt9zvc4z hugoblog]# vi /usr/setup/nginx_1.12.2/conf/nginx.conf
  location / {
      root   /home/project/hugoblog/zeanzai.github.io;
      index  index.html index.htm;
  }
```

7. 编写钩子的执行文件.
关闭webhook服务，然后修改 `/opt/scripts/hugo-deploy/redeploy.sh` 文件

{{< highlight html >}}
# 文件位置：/opt/scripts/hugo-deploy/redeploy.sh
#!/bin/bash

# 1. 开始
time1=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time1} start ... " >> /opt/scripts/hugo-deploy/redeploy.log &&


# 2. 拉取GitHub的article
cd /home/project/hugoblog/zeanzaiblogarticle &&
git pull origin master &&
time2=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time2} git pull article " >> /opt/scripts/hugo-deploy/redeploy.log &&

# 3. 删除io下面所有的文件
cd /home/project/hugoblog/zeanzai.github.io &&
rm -rf 404.html categories css favicon.ico img index.html index.xml js page post readme sitemap.xml tags &&
time3=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time3} remove io " >> /opt/scripts/hugo-deploy/redeploy.log &&

# 4. 重新生成文件
cd /home/project/hugoblog/zeanzaiblogbase &&
hugo &&
time4=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time4} deploy base " >> /opt/scripts/hugo-deploy/redeploy.log &&

# 5. 添加到缓存区中
cd /home/project/hugoblog/zeanzai.github.io &&
git add . &&
time5=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time5} git add io " >> /opt/scripts/hugo-deploy/redeploy.log &&

# 6. 提交
git commit -m "update from aliyun" &&
time6=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time6} git commit io " >> /opt/scripts/hugo-deploy/redeploy.log &&

# 7. push到GitHub
git push origin master &&
time7=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time7} git push io " >> /opt/scripts/hugo-deploy/redeploy.log &&

# 8. 结束
time8=`date "+%Y-%m-%d %H:%M:%S"` &&
echo "${time8} ... end " >> /opt/scripts/hugo-deploy/redeploy.log &&
echo "" >> /opt/scripts/hugo-deploy/redeploy.log

{{< /highlight >}}

8. 在article的GitHub仓库中配置GitHub钩子
9. 完成

参考：

> https://gohugo.io/hosting-and-deployment/hosting-on-github/#github-project-pages
>
> http://www.gohugo.org/post/coderzh-automated-deploy-hugo/
>
> https://blog.csdn.net/zmzwll1314/article/details/77678293
>
> https://blog.github.com/2016-02-01-working-with-submodules/
>
> https://davidauthier.wearemd.com/blog/deploy-using-github-webhooks.html
>
> https://blog.csdn.net/toyijiu/article/details/73611874
> https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E8%AE%B0%E5%BD%95%E6%AF%8F%E6%AC%A1%E6%9B%B4%E6%96%B0%E5%88%B0%E4%BB%93%E5%BA%93
