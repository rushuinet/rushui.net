---
title: "为 Docker 设置代理"
date: 2023-03-05T23:08:39+08:00
keywords: ["docker"]
description: ""
categories: ["技术应用"]
tags: ["docker"]
thumbnail: ""
banner: ""
---

1.  默认情况下这个配置文件夹并不存在，我们要创建它。
```bash
mkdir -p /etc/systemd/system/docker.service.d
```
2.  创建一个文件 /etc/systemd/system/docker.service.d/http-proxy.conf 包含 HTTP_PROXY [环境变量]:
```bash
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/"
```
3.  如果有局域网或者国内的registry，我们还需要使用 NO_PROXY 变量声明一下，比如你可以能国内的[http://daocloud.io]放有镜像:
```bash
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/" "NO_PROXY=localhost,127.0.0.1,daocloud.io"
 ```
4.  刷新systemd配置:
```bash
sudo systemctl daemon-reload
```
5.  用系统命令验证环境变量加上去没:
```bash
systemctl show --property=Environment docker
#Environment=HTTP_PROXY=http://proxy.example.com:80/
 ```

6.  万事俱备，重启docker，在外面的世界遨游吧:
```bash
sudo systemctl restart docker
```