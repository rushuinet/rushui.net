---
title: "K8S从容器复制文件出主机"
date: 2023-03-12T19:40:25+08:00
keywords: ["K8S"]
description: ""
categories: ["技术应用"]
tags: ["K8S"]
thumbnail: ""
banner: ""
---

1.kubectl cp /主机目录/文件路径 podName:/容器路径/xxx.datasource -n namespaces
这样可以把主机目录文件拷贝到容器内

2.kubectl cp podName:容器路径/xxx.datasource -c 容器名称 -n namespaces /主机目录
这样可以把容器内文件cp到主机目录
```bash
kubectl cp productpage-v1-6b746f74dc-hp9jp:usr/local/bin/envoy -c istio-proxy ./envoy
```
从容器拷贝文件到主机的时候podNname:这里不要加/ 之前加了/会一直报错