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

1、安装protoc工具
```bash 
brew install protobuf
protoc --version #最新版本
```
2.安装用于生成go和grpc相关的工具
```bash
	go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
	go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```
3.注意新老版本生成的文件有不兼容的地方。