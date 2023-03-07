---
title: "k8s开启ipvs"
date: 2023-03-07T23:45:13+08:00
keywords: ["k8s"]
description: ""
categories: ["技术应用"]
tags: ["k8s"]
thumbnail: ""
banner: ""
---

## 在所有节点安装ipset和ipvsadm(可选)包
```bash
 yum install ipset -y
 yum install ipvsadm -y
```
## 在所有的Kubernetes节点执行以下脚本:
```
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
# 使用lsmod | grep -e ip_vs -e nf_conntrack_ipv4命令查看是否已经正确加载所需的内核模块。
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```
## 修改配置
修改ConfigMap的kube-system/kube-proxy中的config.conf，把 mode: “” 改为mode: “ipvs” 保存退出即可
```bash
kubectl edit cm kube-proxy -n kube-system
```
## 删除之前的pod
```bash
kubectl get pod -n kube-system |grep kube-proxy |awk '{system("kubectl delete pod "$1" -n kube-system")}'
```
## 查看proxy运行状态
```bash
kubectl get pod -n kube-system | grep kube-proxy
```

#查看日志
如果有 `Using ipvs Proxier.` 说明kube-proxy的ipvs 开启成功!
 ```bash
 kubectl logs -n kube-system kube-proxy-54qnw
```
## 排障
can’t set sysctl net/ipv4/vs/conn_reuse_mode, kernel version must be at least 4.1
说明内核太旧，要升级centos内核版本
https://www.rushui.net/posts/upgrade-centos-kernel-version/