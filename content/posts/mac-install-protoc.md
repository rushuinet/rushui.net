---
title: "Mac 安装 protoc"
date: 2023-05-17T19:37:40+08:00
keywords: ["protoc"]
description: ""
categories: ["技术文档"]
tags: ["protoc"]
thumbnail: ""
banner: ""
---

## 1、安装protoc工具
```bash 
brew install protobuf
protoc --version #最新版本
```
windows用户自己网上找教程
## 2.安装用于生成go和grpc相关的工具
```bash
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
export PATH=$PATH:$GOPATH/bin
```
## 3.使用
```bash
protoc --proto_path=. --go_out=paths=source_relative:. --go-grpc_out=paths=source_relative:. $(find pb -name *.proto)
```
source_relative 表示生成在.proto文件的同目录下

## 参考项目：
https://github.com/grpc-example/simple


- 注意老版本生成的文件有不兼容的地方。