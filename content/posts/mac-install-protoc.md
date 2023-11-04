---
title: "Mac 安装 protoc 及代码生成插件"
date: 2023-05-17T19:37:40+08:00
keywords: ["protoc"]
description: ""
categories: ["技术文档"]
tags: ["protoc"]
thumbnail: ""
banner: ""
---

## 1、安装 protoc 工具

```bash
brew install protobuf
protoc --version #最新版本
```

windows 用户自己网上找教程

## 2.安装用于生成 go 和 grpc 相关的工具

```bash
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
export PATH=$PATH:$GOPATH/bin
```

## 3.使用

```bash
protoc --proto_path=. --go_out=paths=source_relative:. --go-grpc_out=paths=source_relative:. $(find pb -name *.proto)
```

source_relative 表示生成在.proto 文件的同目录下

## 生成 javat 和 ts

### java

下载 protoc-gen-grpc-java: https://repo.maven.apache.org/maven2/io/grpc/protoc-gen-grpc-java/1.56.0/

以 mac aarch 为例：

```bash
curl -O https://repo.maven.apache.org/maven2/io/grpc/protoc-gen-grpc-java/1.56.0/protoc-gen-grpc-java-1.56.0-osx-aarch_64.exe
mv protoc-gen-grpc-java-1.58.0-osx-aarch_64.exe /usr/local/bin/protoc-gen-grpc-java
chmod +x /usr/local/bin/protoc-gen-grpc-java

# 使用
protoc --proto_path=.  --grpc-java_out=paths=source_relative:. $(find pb -name *.proto)
```

### ts

```bash
sudo npm install -g protoc-gen-ts

# 使用
protoc --proto_path=.  --ts_out=paths=source_relative:. $(find pb -name *.proto)
```

## 参考项目：

https://github.com/grpc-example/simple

- 注意老版本生成的文件有不兼容的地方。
