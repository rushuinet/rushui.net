---
title: "Grpc 流式传输Stream Demo"
date: 2023-08-10T20:04:41+08:00
keywords: ["grpc"]
description: "grpc 流式传输 demo 服务端流、客户端流、双向流"
categories: ["技术应用"]
tags: ["grpc"]
thumbnail: ""
banner: ""
---

## 说明
grpc 流式传输 demo 服务端流、客户端流、双向流

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
git clone https://github.com/grpc-example/stream.git
cd stream
go mod tidy
```
## 编译.proto文件命令
```bash
make api
go mod tidy
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
https://github.com/grpc-example/stream