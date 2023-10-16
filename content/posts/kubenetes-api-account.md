---
title: "kubernetes API 访问控制"
date: 2023-10-11T20:03:19+08:00
keywords: ["k8s"]
description: "用户使用 kubectl、客户端库或构造 REST 请求来访问 Kubernetes API。 人类用户和 Kubernetes 服务账号都可以被鉴权访问 API。"
categories: ["技术文档"]
tags: ["k8s"]
thumbnail: ""
banner: ""
---

## 概念
用户使用 kubectl、客户端库或构造 REST 请求来访问 Kubernetes API。 人类用户和 Kubernetes 服务账号都可以被鉴权访问 API。 当请求到达 API 时，它会经历多个阶段：认证 -> 鉴权 -> 准入控制。

### 两种场景
第一个部分是人机交互的过程。 是大家非常熟悉的用 kubectl 对 apiserver 的一个请求过程；

第二个部分是 Pod 中的业务逻辑与 apiserver 之间的交互。
### 两种用户
- Users Accounts普通账户 （人机交互，如kubectl）
- 由k8s管理的Service Accounts 服务帐号（内部服务：pod）
### UserAccount 与 ServiceAccount
- 用户账户是针对人而言的。 服务账户是针对运行在 pod 中的进程而言的。
- 用户账户是全局性的。 其名称在集群各 namespace 中都是全局唯一的，未来的用户资源不会做 namespace 隔离， 服务账户是namespace 隔离的。
- 通常情况下，集群的用户账户可能会从企业数据库进行同步，其创建需要特殊权限，并且涉及到复杂的业务流程。服务账户创建的目的是为了更轻量，允许集群用户为了具体的任务创建服务账户 ( 即权限最小化原则 )。
## 示例
### 在集群外部进行身份验证
https://github.com/kubernetes/client-go/tree/master/examples/out-of-cluster-client-configuration

### 在集群内部进行身份验证
https://github.com/kubernetes/client-go/tree/master/examples/in-cluster-client-configuration

## 官方文档
https://kubernetes.io/zh-cn/docs/concepts/security/controlling-access/