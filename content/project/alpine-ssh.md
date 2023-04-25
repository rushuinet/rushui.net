---
title: "alpine 版本的ssh服务"
date: 2023-04-25T22:18:10+08:00
keywords: ["alpine","ssh"]
description: "alpine 版本的ssh服务，在做webssh项目或者做ssh登录测试时，如果拿正式的服务器测试会有安全隐患，使用docker版本的ssh服务，可快速启动多个轻量级的虚拟linux系统。"
categories: ["开源项目"]
tags: ["alpine","ssh"]
thumbnail: ""
banner: ""
---
## 说明
alpine 版本的ssh服务，在做webssh项目或者做ssh登录测试时，如果拿正式的服务器测试会有安全隐患，使用docker版本的ssh服务，可快速启动多个轻量级的虚拟linux系统。

在`alpine`基础上增加了 `openssh` `tzdata` `bash`

默认账号密码 `root:123456`

## 启动
```bash
docker run -d -p 222:22 --rm bimg/alpinessh
```
## 通过ssh 客户端连接SSH服务容器
```bash
ssh -p 222 root@127.0.0.1
```
输入密码`123456`即可登录
## 设置免密登录
```bash
ssh-copy-id -p 222 root@127.0.0.1
ssh -p 222 root@127.0.0.1
```
## 项目地址
https://github.com/basicimage/alpinessh
