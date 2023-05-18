---
title: "Grpc 手动模式的服务发现及轮询负载均衡demo"
date: 2023-05-18T15:02:31+08:00
keywords: ["grpc","服务发现","负载均衡"]
description: "对于单个服务来说，手动指定服务的ip端口就可以直接调用了，当服务启动多个实例的时候我们就无法具体指定了，一般服务发现与负载均衡是分不开的..."
categories: ["技术应用"]
tags: ["grpc","服务发现","负载均衡"]
thumbnail: ""
banner: ""
---
## 说明
对于单个服务来说，手动指定服务的ip端口就可以直接调用了，当服务启动多个实例的时候我们就无法具体指定了，一般服务发现与负载均衡是分不开的，在微服务架构下这些都是必须的组件。本demo只是手动情况下的服务发现，后续会实现与注册中心结合的自动服务发现。
## 准备工作
[Mac 安装 protoc]({{<url "posts/mac-install-protoc" >}})

## 下载demo
```bash
git clone https://github.com/grpc-example/resolver.git
cd resolver
go mod tidy
```
## 启动两个服务端
```bash
go run server.go --port=50001
go run server.go --port=50002
```
## 启动客户端
```bash
go run client.go
curl http://localhost:8081/hello
```
多次curl调用，可以看到client的请求轮询发送到服务端
## 说明
client上启动了一个http服务，做为流量入口，然后通过grpcClient去请求grpc服务。
- 这里使用了grpc的manual(手动模式)包，自定义ds的scheme
- m.InitialState 时定义了两个服务的地址
- m.UpdateState 10s后更新成不存在的地址，所以后面请求就失败了

## 项目地址
https://github.com/grpc-example/resolver