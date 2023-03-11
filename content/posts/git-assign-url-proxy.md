---
title: "git指定网址使用代理"
date: 2023-03-09T23:18:10+08:00
keywords: ["git"]
description: ""
categories: ["技术应用"]
tags: ["git"]
thumbnail: ""
banner: ""
---
## 指定网址
```bash
vim ~/.gitconfig
[http "https://github.com"]
    proxy = socks5://127.0.0.1:1080
```
## 设置代理
如果你有国外云主机的话可用ssh搭建临时通道
```bash
ssh -N -f -D 0.0.0.0:1080 -p 22 root@192.168.xx.xxx
```
## 设置免密登录
```bash
1.进入.ssh目录： cd ~/.ssh
2.生成一对密钥： ssh-keygen -t rsa
3.发送公钥：     ssh-copy-id -p 22 root@192.168.xx.xxx
4.免密登录测试： ssh -p 22 root@192.168.xx.xxx
```