---
title: "一个Kubebuilder的简单demo"
date: 2023-06-06T20:03:04+08:00
keywords: ["k8s",Kubebuilder"]
description: ""
categories: ["技术应用"]
tags: ["k8s","Kubebuilder"]
thumbnail: ""
banner: ""
---
## 安装kubebuilder
安装文档：
https://book.kubebuilder.io/quick-start.html

快捷命令：
```bash
# download kubebuilder and install locally.
curl -L -o kubebuilder https://go.kubebuilder.io/dl/latest/$(go env GOOS)/$(go env GOARCH)
chmod +x kubebuilder && mv kubebuilder /usr/local/bin/
# 查看是否安装成功
kubebuilder version
```

## 自动补全
在 `~/.bash_profile` 或 `~/.bashrc`下增加以下内容
```bash
# kubebuilder autocompletion
echo 'source <(kubebuilder completion bash)' >>~/.bash_profile
source ~/.bash_profile
kubebuilder completion bash >/opt/homebrew/etc/bash_completion.d/kubebuilder

# 增加kb简写自动补全
vim ~/.bash_profile
alias kb=kubebuilder
complete -o default -F __start_kubebuilder kb
source ~/.bash_profile
```
重启命令行即可生效
## 快速体验
```bash
# 初始化项目
kubebuilder init --domain github.com --repo github.com/kubebuilder-demo/simple

# 创建api
kubebuilder create api --group kubebuilder-demo --version v1 --kind Simple
# 生成crd
make manifests
# 安装crd到k8s集群,这里用于kustomize，如果没有会自动下载到./bin/，这里下载kustomize可能被qiang自己想办法
make install
# 查看crd是否安装成功：simples.kubebuilder-demo.github.com
kubectl get crd

# 启动controller
make run

# 部署cr
kubectl apply -f config/samples/kubebuilder-demo_v1_simple.yaml
# 查看simple的cr是否部署成功
kubectl get simple

# 打包docker镜像
# 这里发现 Dockerfile 里的 gcr.io/distroless/static:nonroot 镜像被qiang了，改为：devk8s/gcr-io_distroless_static:nonroot
# Dockerfile golang builder 时要加 ENV GOPROXY="https://goproxy.cn,direct" 
# 用docker-build时，k8s机器上报standard_init_linux.go:228: exec user process caused: exec format error，平台不兼容导致所以用docker-buildx
make docker-buildx IMG=kubebuilderdemo/simple:0.0.1

# 部署到k8s集群
# config/default/manager_auth_proxy_patch.yaml下的 
# gcr.io/kubebuilder/kube-rbac-proxy:v0.13.1 改为 devk8s/gcr-io_kubebuilder_kube-rbac-proxy:v0.13.1
make deploy IMG=kubebuilderdemo/simple:0.0.1
# 查看资源是否就绪
kubectl -nsimple-system get svc
kubectl -nsimple-system get deploy
kubectl -nsimple-system get pod

# pod启动不了的几个调试命令
 kubectl -nsimple-system describe pod pod名称
 kubectl -nsimple-system logs  pod名称 --all-containers
 kubectl -nsimple-system logs pod名称 -c 容器名称
 kubectl -nsimple-system get event
 kubectl -nsimple-system get events --field-selector involvedObject.name=pod名称

# 卸载crd
make uninstall
# 反部署
make undeploy
```

## 注意点
- go和docker镜像有些可能被qiang这块要处理
- Dockerfile golang builder 时要加 ENV GOPROXY="https://goproxy.cn,direct"
- Dockerfile 里的 gcr.io/distroless/static:nonroot 镜像被qiang了，改为：devk8s/gcr-io_distroless_static:nonroot
- config/default/manager_auth_proxy_patch.yaml下的 gcr.io/kubebuilder/kube-rbac-proxy:v0.13.1 改为 devk8s/gcr-io_kubebuilder_kube-rbac-proxy:v0.13.1
- 如果在苹果芯片上build用make docker-buildx（多平台build）参考：[Docker使用Buildx构建多平台镜像]({{<url "posts/docker-buildx" >}})

## 项目地址
https://github.com/kubebuilder-demo/simple