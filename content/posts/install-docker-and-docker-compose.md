---
title: "安装docker和docker-compose"
date: 2023-03-05T23:04:42+08:00
keywords: ["docker","docker-compose","容器"]
description: "安装docker和docker-compose"
categories: ["技术应用"]
tags: ["docker","docker-compose","容器"]
thumbnail: "images/docker_logo.jpg"
banner: ""
---
### 安装docker
```bash
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#yum -y install yum-utils 可解决yum-config-manager不存在的问题
yum -y install docker-ce
systemctl start docker
chkconfig docker on
```

### 修改数据目录
```bash
#展示当前docker的配置信息
docker info
默认路径：/var/lib/docker

{
  "registry-mirrors": ["https://ddcfwvrh.mirror.aliyuncs.com"],
  "data-root": "/data/docker"
}

sudo systemctl restart docker
```

### 设置 docker/compose
```bash
sudo curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

# 命令补全
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.27.4/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
source /etc/bash_completion.d/docker-compose

```

### 设置docker的golang 环境
```bash
docker pull golang:alpine
#用法，直接运行golang程序
docker run -d  -v /root/work:/data -p 8081:8081 golang:alpine go run /data/main.go

```