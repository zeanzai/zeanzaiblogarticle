---
title: "Pig开源框架学习之环境搭建"
date: 2018-10-17T16:22:57+08:00
description: "PIG开源框架学习之环境搭建"
keywords: "pig"
categories:
  - "pig"
tags:
  - "install"
---

## 1. redis的安装
### 1.1 下载window版本redis

之前<a href="https://redis.io/" target="_blank">redis的官网</a>上面有window版本的redis安装包，现在没有了，只能从GitHub上面下载：<a href="https://github.com/MicrosoftArchive/redis/releases/download/win-3.2.100/Redis-x64-3.2.100.zip" target="_blank">下载地址</a>。

下载后文件名为： Redis-x64-3.2.100.zip

### 1.2 安装、配置与测试

<ol>
  <li>解压 Redis-x64-3.2.100.zip 到安装目录即可。</li>
  <p>我的解压地址为： <code>D:\packages\devtools\Redis-x64-3.2.100</code>；解压完成后，即可在命令行中启动redis，但是命令行窗口关闭，redis就会关闭；因此，需要将redis配置为系统服务。</p>

  <li>配置Redis服务</li>
  <p>在 file explore 的地址栏内，输入cmd打开cmd.exe命令行。</p>
  <img src="/img/pig/001.png" /><br /><br />
  <img src="/img/pig/002.png" /><br /><br />
  <p>在命令行中输入 <code>redis-server --service-install redis.windows-service.conf --loglevel verbose</code>完成服务的注册</p>
  <img src="/img/pig/003.png" /><br /><br />
  <img src="/img/pig/004.png" /><br /><br />

  <li>常用的redis服务命令</li>
  <ul>
    <li>卸载服务：<code>redis-server --service-uninstall</code></li>
    <li>开启服务：<code>redis-server --service-start</code></li>
    <li>停止服务：<code>redis-server --service-stop</code></li>
  </ul>

  <li>启动服务并测试</li>
  <img src="/img/pig/005.png" /><br /><br />

</ol>

---

## 2. rabbitmq的安装
### 2.1 erlang的安装
<ol>
  <li>下载erlang的安装包</li>
  <p><a href="http://www.erlang.org/downloads" target="_blank">下载地址</a></p>
  <img src="/img/pig/006.png" /><br /><br />

  <li></li>
  <p></p>
</ol>
