---
title: "docker对外提供服务"
date: 2023-03-23T23:07:32+08:00
keywords: ["docker"]
description: ""
categories: ["技术应用"]
tags: ["docker"]
thumbnail: ""
banner: ""
---

## 编辑服务配置文件
```bash
vi /lib/systemd/system/docker.service
```
## 修改以ExecStart开头的行：(因为我的系统是centos 7的，所以修改为下面得)
```bash
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
```
如果是centos7以下的话，就把ExecStart修改为：
```bash
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375
```
## 修改后保存文件，然后通知docker服务做出的修改
```bash
systemctl daemon-reload
service docker restart
```

## 测试
```bash
curl http://localhost:2375/info
```
## API
```bash
# 镜像列表
http://10.192.10.161:2375/images/json
# 容器列表
http://10.192.10.161:2375/containers/json

# 官方文档
https://docs.docker.com/engine/api/v1.24/

https://docs.docker.com/engine/api/v1.40

# 外部命令行访问
docker -H IP:2375 images