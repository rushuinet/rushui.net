---
title: "gomod使用ssh地址的私库"
date: 2023-03-08T23:29:26+08:00
keywords: ["git","golang"]
description: ""
categories: ["技术应用"]
tags: ["git","golang"]
thumbnail: ""
banner: ""
---

设置git配置http转ssh
```bash
vim ~/.gitconfig
增加:
[url "git@git.demo.com:"]
       insteadOf = http://git.demo.com
```
设置go mod代理
```bash
GOPROXY=https://goproxy.cn,direct
```
设置不走代理的私库地址
```bash
go env -w GONOSUMDB=git.demo.com
go env -w GONOPROXY=git.demo.com
```