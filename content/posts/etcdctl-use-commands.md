---
title: "etcdctl常用命令"
date: 2023-09-05T20:26:09+08:00
keywords: ["etcd","etcdctl"]
description: ""
categories: ["技术应用"]
tags: ["etcd","etcdctl"]
thumbnail: ""
banner: ""
---
## etcdctl -h
```bash
VERSION:
	3.4.27

API VERSION:
	3.4
```
## 集群
### 查看集群状态
```bash
export ENDPOINTS="192.168.5.41:2379,192.168.5.45:2379,192.168.5.46:2379"
etcdctl --write-out=table --endpoints=$ENDPOINTS endpoint status
```
### 查看集群成员
```bash
etcdctl --write-out=table --endpoints=$ENDPOINTS member list
```
### 删除成员
```bash
MEMBER_ID=fa6333c794b010d8
etcdctl --endpoints=$ENDPOINTS member remove ${MEMBER_ID}
```
### 添加成员(添加已经删除的需要将新节点下面data.etcd必须删除)
```bash
export HOST_1=192.168.5.41
export HOST_2=192.168.5.45
export HOST_3=192.168.5.46
export NAME_1=etcd1
export NAME_2=etcd2
export NAME_3=etcd3
etcdctl --endpoints=${HOST_1}:2379,${HOST_2}:2379 member add ${NAME_3} --peer-urls=http://${HOST_3}:2380
export TOKEN=etcd-cluster-3
export ADVERTISE_PRRE_URLS=https:$HOST_3:2380
```
### 在新加节点上面，执行命令：
```bash
export NAME=etcd3
export CLINE_URLS="https://192.168.5.46:2379,https://192.168.5.46:4001"
export CLUSTER=$NAME_1="https:$HOST_1:2380,https://$HOST_1:4001",$NAME_2="https:$HOST_2:2380,https://$HOST_2:4001"
export ADVERTISE_PRRE_URLS=https:$HOST_3:2380

#/usr/local/bin/etcd 
--peer-client-cert-auth 
--client-cert-auth 
--data-dir=/var/lib/rancher/etcd/ 
--advertise-client-urls=$CLINE_URLS
--key-file=/etc/kubernetes/ssl/kube-etcd-192-168-5-46-key.pem
--peer-cert-file=/etc/kubernetes/ssl/kube-etcd-192-168-5-46.pem
--peer-key-file=/etc/kubernetes/ssl/kube-etcd-192-168-5-46-key.pem
--election-timeout=5000 
--name=$NAME 
--listen-peer-urls=https://0.0.0.0:2380 
--initial-cluster=$CLUSTER 
--initial-cluster-state=existing 
--initial-cluster-token=$TOKEN
--listen-client-urls=https://0.0.0.0:2379
--heartbeat-interval=500 
--initial-advertise-peer-urls=$ADVERTISE_PRRE_URLS
--trusted-ca-file=/etc/kubernetes/ssl/kube-ca.pem
--peer-trusted-ca-file=/etc/kubernetes/ssl/kube-ca.pem
--cert-file=/etc/kubernetes/ssl/kube-etcd-192-168-5-46.pem
```
## put操作
```bash
etcdctl --endpoints=$ENDPOINTS put testkey "Hello World"
```
## get操作
### 得到所有的key(这里是前缀匹配)
```bash
etcdctl --endpoints=$ENDPOINTS  --prefix --keys-only=true get / 
```
### 得到对应key的值
```bash
etcdctl --endpoints=$ENDPOINTS  get /testkey
/testkey
hello world
```
## del操作
```bash
etcdctl --endpoints=$ENDPOINTS del /testkey
etcdctl --endpoints=$ENDPOINTS get /testkey
```
## watch 操作
```bash
etcdctl --endpoints=$ENDPOINTS watch /testkey
PUT
/testkey
1234
etcdctl --endpoints=$ENDPOINTS put /testkey "1234"
```
## defrag(磁盘碎片整理)
```bash
etcdctl defrag --data-dir default.etcd
etcdctl --endpoints=$ENDPOINTS defrag 
```
## snapshot
保存备份当前集群etcd的信息，用于新的etcd集群
```bash
# save
etcdctl snapshot save snapshot.db
ls
# restore
etcdctl snapshot save snapshot.db

# restore members
bin/etcdctl snapshot restore snapshot.db --initial-cluster-token etcd-cluster-1 --initial-advertise-peer-urls http://127.0.0.1:12380  --name sshot1 --initial-cluster 'sshot1=http://127.0.0.1:12380,sshot2=http://127.0.0.1:22380,sshot3=http://127.0.0.1:32380'
bin/etcdctl snapshot restore snapshot.db --initial-cluster-token etcd-cluster-1 --initial-advertise-peer-urls http://127.0.0.1:22380  --name sshot2 --initial-cluster 'sshot1=http://127.0.0.1:12380,sshot2=http://127.0.0.1:22380,sshot3=http://127.0.0.1:32380'
bin/etcdctl snapshot restore snapshot.db --initial-cluster-token etcd-cluster-1 --initial-advertise-peer-urls http://127.0.0.1:32380  --name sshot3 --initial-cluster 'sshot1=http://127.0.0.1:12380,sshot2=http://127.0.0.1:22380,sshot3=http://127.0.0.1:32380'

# launch members
bin/etcd --name sshot1 --listen-client-urls http://127.0.0.1:2379 --advertise-client-urls http://127.0.0.1:2379 --listen-peer-urls http://127.0.0.1:12380 &
bin/etcd --name sshot2 --listen-client-urls http://127.0.0.1:22379 --advertise-client-urls http://127.0.0.1:22379 --listen-peer-urls http://127.0.0.1:22380 &
bin/etcd --name sshot3 --listen-client-urls http://127.0.0.1:32379 --advertise-client-urls http://127.0.0.1:32379 --listen-peer-urls http://127.0.0.1:32380 &
# status
etcdctl snapshot status snapshot.db
etcdctl snapshot status snapshot.db  -w table
```
## 查看当前endpoint的status
```bash
etcdctl --endpoints=$ENDPOINTS endpoint --cluster=true status  -w table
```
## move-leader
```bash
etcdctl --endpoints 192.168.5.45:2379  move-leader d6414a7c7c550d29
```
## 访问控制相关
```bash
# 添加root用户
etcdctl user add root
# 添加非root用户
etcdctl user add wang
# 添加role
etcdctl role add role1
# 将用户wang加入角色role1中
etcdctl user grant-role wang role1
# 给角色role1复制权限
etcdctl role grant-permission role1 read /testkey
# 开启用户认证
etcdctl --endpoints=$ENDPOINTS auth enable
# 验证权限(可以读，但是不能写)
etcdctl  put /testkey "1111"  --user="wang:111111" 
Error: etcdserver: permission denied
etcdctl  get /testkey  --user="wang:111111" 
/testkey
111111
```
## ui界面
```bash
docker run --rm --name=etcdv3-browser -p 9980:80 joinsunsoft/etcdv3-browser:1.0.0

http://localhost:9980/main.html#
Username: ginghan Password: 123456
```