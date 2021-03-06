---
title: "前后端分离框架中验证码的问题"
date: 2018-10-09T15:34:06+08:00
description: "前后端分离框架中验证码的问题"
keywords: "前后端分离 验证码"
categories:
  - "Dev"
tags:
  - "DevOps"
---

## 1. 所遇到的问题
### 1.1 验证码唯一性
前后端分离会遇到一个问题就是不再依赖session，前端的请求是无状态的，如何保证A请求的验证码是A请求的不是B请求的？
例如：A请求的验证码是‘123’，B请求的验证码是‘456’。这是验证码的唯一性
### 1.2 验证码的校验
前后端分离后无法使用session，那如何确定用户输入的验证码就是后端返回给用户的验证码，这就所说的验证码的校验。
### 1.3 校验思路
解决验证码的校验的思路大概有这么几种：前端校验、后端校验和前后校验
#### 1.3.1 前端校验
是指后端在将验证码图片传给前端的同时也会把验证码上面的字符也传给前端，然后由前端根据后端传给的验证码和用户输入的验证码做对比校验。
#### 1.3.2 后端校验
后端校验是指后端生成验证码图片的同时记录图片上面的验证码，然后由前端传来的验证码和后端存储的验证码进行对比校验，此方式有一个问题就是要保证验证码的唯一性。
#### 1.3.3 前后校验
是指既有前端校验又有后端校验。

## 2. 解决方案
### 2.1 方案一：后端验证
前端在请求验证码的时候加一个随机数参数，后台接收到这个随机数参数后，将这个随机数和验证码保存到redis中。验证时，需要前端把随机数也要传过来，后端去redis中去查即可。
### 2.2 方案二：后端验证
如果是前后端分离，那么，前端每次请求都是无状态的，那么，就需要在前端第一次请求的时候，分配给前端一个token，然后，前端每次请求时，都会带着这个token。可以将该token作为redis的键值，并将验证码放在对应的值位置。
### 2.3 方案三：前端验证
后端Response Header里面返回一个id的字段，和验证码的值相关联起来，前端读取返回的Header中的id然后跟用户输入的进行验证。
