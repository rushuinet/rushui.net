---
title: "Docker 修改容器端口"
date: 2023-03-23T23:12:37+08:00
keywords: ["docker"]
description: ""
categories: ["技术应用"]
tags: ["docker"]
thumbnail: ""
banner: ""
---

# 修改容器配置文件
修改前需要关闭docker，否则镜像重启后，配置文件还原导致修改失败
```bash
systemctl stop docker
```
### 1. 进入容器配置目录
```bash
cd /var/lib/docker/containers/{container_id}
```

### 2. 修改hostconfig.json
```bash
vim hostconfig.json
```
找到PortBindings节点，结构如下
```bash
{
    "PortBindings": {
    	"80/tcp": [
	        {
				"HostIp": "",
				"HostPort": "81"
			}
    	]
    }
}
```
这个节点的含义是将服务器的81端口映射到容器的80端口

### 3. config.v2.json文件
这个文件可以不改
### 启动docker 
```bash
systemctl start docker
```
### 启动所有容器(可以多次运行)
```bash
docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)
```