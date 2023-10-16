---
title: "mac安装kubectl并设置命令自动补全"
date: 2023-04-24T20:29:57+08:00
keywords: ["k8s","kubectl"]
description: ""
categories: ["技术文档"]
tags: ["k8s","kubectl"]
thumbnail: ""
banner: ""
---

## 安装
文档：[https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-macos/](https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-macos/)

我的是m1命令为：
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

## 命令补全
命令补全配置见：[mac下docker命令自动补齐]({{<url "posts/docker-completion" >}})

1.在文件 `~/.bash_profile` 中导入（Source）补全脚本：
```bash
echo 'source <(kubectl completion bash)' >>~/.bash_profile
```
2.将补全脚本添加到目录 `/opt/homebrew/etc/bash_completion.d/`(有的不是此目录) 中：
```bash 
kubectl completion bash >/opt/homebrew/etc/bash_completion.d/kubectl
```
3.如果你为 `kubectl` 定义了别名，则可以扩展 Shell 补全来兼容该别名：
```bash
echo 'alias k=kubectl' >>~/.bash_profile
echo 'complete -o default -F __start_kubectl k' >>~/.bash_profile
source ~/.bash_profile
```
重新加载 Shell 之后，kubectl 补全功能将立即生效。
