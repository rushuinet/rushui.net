---
title: "Docker Registry"
date: 2023-03-27T21:45:08+08:00
keywords: ["docker"]
description: ""
categories: ["技术应用"]
tags: ["docker"]
thumbnail: ""
banner: ""
---
1. 运行下面命令获取registry镜像
```bash
docker pull registry
```
2. 将registry镜像运行并生成一个容器
```bash
docker run -d -v /data/registry:/var/lib/registry -p 5000:5000 --restart=always --name registry registry:latest
```
3. 访问 http://localhost:5000/v2/

4.查看镜像：http://localhost:5000/v2/_catalog

http://localhost:5000/v2/image_name/tags/list

5.打包镜像及推送
```bash
docker build -t 192.168.41.131:5000/rushui/webdemo .
docker push 192.168.41.131:5000/rushui/webdemo
```
6. 出现如下错误解决：
```bash
Get https://192.168.41.131:5000/v2/: http: server gave HTTP response to HTTPS client

vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://ddcfwvrh.mirror.aliyuncs.com"],
  "insecure-registries": ["192.168.41.131:5000"]
}
systemctl restart docker
```