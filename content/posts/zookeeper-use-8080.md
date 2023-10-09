---
title: "解决zookeeper占用8080端口"
date: 2023-10-08T20:00:09+08:00
keywords: ["zookeeper"]
description: "8080是很多程序默认的一个端口，使用这个端口会经常造成端口冲突。以zookeeper为例分享如何解决"
categories: ["技术应用"]
tags: ["zookeeper"]
thumbnail: ""
banner: ""
---
## 说明
8080是很多程序默认的一个端口，使用这个端口会经常造成端口冲突。下面以zookeeper为例：

安装zookeeper后, 发现端口8080被占用, 分享如何解决.
zookeeper部署后, 3.5以后的版本, 会自动占用8080端口. 需要修改配置文件.
## 检测
```bash
a,查看端口占用
  netstat -nap|grep 8080
b,查看端口占用的进程
    ps -aux |grep 27672
发现, 8080端口被zookeeper占用. 
```
## 修改
修改配置文件
```bash
vim /usr/local/zookeeper/zookeeper-3.5.8/conf/zoo.cfg
添加内容或者修改内容
admin.serverPort=8887
```
重启动zk即可.