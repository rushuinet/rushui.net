---
title: "使用 grpcurl 通过命令行访问 gRPC 服务"
date: 2023-08-24T23:19:17+08:00
keywords: ["grpc","grpcurl"]
description: "一般情况下测试 gRPC 服务，都是通过客户端来直接请求服务端。如果客户端还没准备好的话，也可以使用 BloomRPC 这样的 GUI 客户端。"
categories: ["技术应用"]
tags: ["grpc","grpcurl"]
thumbnail: ""
banner: ""
---
## 说明
一般情况下测试 gRPC 服务，都是通过客户端来直接请求服务端。如果客户端还没准备好的话，也可以使用 BloomRPC 这样的 GUI 客户端。
如果环境不支持安装这种 GUI 客户端的话，那么有没有一种工具，类似于 curl 这样的，直接通过终端，在命令行发起请求呢？
答案肯定是有的，就是本文要介绍的 grpcurl。

## grpc服务
使用之前写的示例项目：https://github.com/grpc-example/simple

关键代码：
```go
// 注册 grpcurl 所需的 reflection 服务
reflection.Register(server)
```

## 使用grpcurl
查看服务列表
```bash
grpcurl -plaintext 127.0.0.1:50001 list

grpc.reflection.v1alpha.ServerReflection
hello.Greeter
```
服务的方法列表
```bash
grpcurl -plaintext 127.0.0.1:50001 list hello.Greeter

hello.Greeter.SayHello
```
查看方法定义
```bash
grpcurl -plaintext 127.0.0.1:50001 describe hello.Greeter.SayHello

hello.Greeter.SayHello is a method:
rpc SayHello ( .hello.HelloRequest ) returns ( .hello.HelloReply );
```
查看请求参数
```bash
grpcurl -plaintext 127.0.0.1:50001 describe hello.HelloRequest

message HelloRequest {
  string name = 1;
}
```
请求服务
```bash
grpcurl -d '{"name": "wang"}' -plaintext 127.0.0.1:50001 hello.Greeter.SayHello

{
  "message": "Hello wang"
}
```
-d 参数后面也可以跟 @，表示从标准输入读取 json 参数，一般用于输入比较复杂的 json 数据，也可以用于测试流方法。

注意：想结束输入时，需要先输入回车，然后输入ctrl+D