---
title: "kubectl 常用命令简写"
date: 2023-03-05T23:44:33+08:00
keywords: ["kubectl","k8s"]
description: ""
categories: ["技术应用"]
tags: ["kubectl","k8s"]
thumbnail: ""
banner: ""
---

```bash
alias k="kubectl"
alias ks="kubectl -n kube-system"
alias ki="kubectl -n istio-system"
alias kls="kubectl config get-contexts"
alias kuse="kubectl config use-context"
alias kns='kns(){ kubectl config set-context $(kubectl config current-context) --namespace=$1;};kns'
```