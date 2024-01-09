---
title: "K8s 证书过期"
date: 2024-01-08T19:55:51+08:00
keywords: ["k8s"]
description: "K8S访问时报错：之Unable to connect to the server: x509: certificate has expired or is not yet valid"
categories: ["技术应用"]
tags: ["k8s"]
thumbnail: ""
banner: ""
---

## 问题
今天请求k8s集群的时候出现如下报错：
```bash
kubectl get pods
Unable to connect to the server: x509: certificate has expired or is not yet valid: current time 2024-01-08T15:28:17+08:00 is after 2024-01-03T09:22:03Z
```
## 解决方案
```bash
# 备份 kubernetes配置
cp -r /etc/kubernetes  /etc/kubernetes_bak
# 检测证书过期
kubeadm certs check-expiration
# 更新证书
kubeadm certs renew all
```
- 检测证书过期时会发现很多证书都是<invalid>的状态
- 更新证书后，证书过期时间已经更新为365d
## 出现新的问题
```bash
kubectl get node
error: You must be logged in to the server (Unauthorized)
```
## 解决方案
```bash
# 备份配置文件
cp -rp $HOME/.kube/config $HOME/.kube/config.bak
# 生成新的配置文件
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
执行kubectl get node查看解决结果，可以正常访问了
## 出现新的问题2
部署一个示例pod:simple-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-simple
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```
```bash
kubectl apply simple-pod-yaml
```
查看发现资源就挂在那里不运行
## 解决方案
1. 重启kubelet
```bash
systemctl restart kubelet
```
2. 重启kube-apiserver、kube-controller-manage、kube-scheduler
```bash
# 如果是docker作为容器的话，可执行如下命令。其余容器方法类似
docker ps |grep kube-apiserver|grep -v pause|awk '{print $1}'|xargs -i docker restart {}
docker ps |grep kube-controller-manage|grep -v pause|awk '{print $1}'|xargs -i docker restart {}
docker ps |grep kube-scheduler|grep -v pause|awk '{print $1}'|xargs -i docker restart {}
```

3. 重新部署示例服务即可(不重新部署也会正常运行)