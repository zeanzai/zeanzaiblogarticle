---
title: "java中的字符串操作"
date: 2018-08-31T10:54:45+08:00
description: "java中的字符串操作"
thumbnail: "img/tn.png"
categories:
  - "Java"
  - "String"
tags:
  - "Java"
---

> 在java.lang包中有String.split()方法，返回是一个数组，但是有很多坑。

# 转义字符得加转义

如果用“.”作为分隔的话，必须是如下写法：`String.split("\\.")`。这样才能正确的分隔开，不能用`String.split(".")`；

如果用“|”作为分隔的话，必须是如下写法：`String.split("\\|")`。这样才能正确的分隔开，不能用`String.split("|")`;
