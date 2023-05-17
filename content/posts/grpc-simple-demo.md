---
title: "一个Grpc 的简单 Demo"
date: 2023-05-17T22:43:56+08:00
keywords: ["grpc"]
description: "gRPC是一种用于构建分布式应用和服务的开源RPC框架。它最初由 Google 开发，旨在解决 RPC（Remote Procedure Call，远程过程调用）在分布式系统中的性能和可靠性问题。"
categories: ["技术应用"]
tags: ["grpc"]
thumbnail: ""
banner: ""
---
## 介绍
gRPC是一种用于构建分布式应用和服务的开源RPC框架。它最初由 Google 开发，旨在解决 RPC（Remote Procedure Call，远程过程调用）在分布式系统中的性能和可靠性问题。gRPC 具有高性能、高可靠性、易于使用和灵活性等特点，已经成为许多企业和开发者的首选框架。
## 准备工作
```bash
brew install protobuf
protoc --version #最新版本
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
export PATH=$PATH:$GOPATH/bin
```
## 下载demo
```bash
git clone https://github.com/grpc-example/simple.git
cd simple
go mod tidy
```
## 编译.proto文件命令
```bash
make api
```

## run server
```bash
go run server.go
```

## run client
```bash
go run client.go
```

## 项目地址
https://github.com/grpc-example/simple