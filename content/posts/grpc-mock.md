---
title: "Grpc Mock"
date: 2023-03-27T21:42:30+08:00
keywords: ["grpc","mock"]
description: ""
categories: ["技术应用"]
tags: ["grpc","mock"]
thumbnail: ""
banner: ""
---
## 项目地址
https://github.com/tokopedia/gripmock

## 编写proto
vim /home/monitor/pd/hello.proto
```proto
syntax = "proto3";

package hello;

// The greeting service definition.
service Greeter {
    // Sends a greeting
    rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
    string name = 1;
}

// The response message containing the greetings
message HelloReply {
    string message = 1;
}
```
## 启动mock服务
```bash
docker run -p 4770:4770 -p 4771:4771 -v /home/monitor/pd:/proto tkpd/gripmock /proto/hello.proto
```
## 建立mock接口数据
```bash
curl -X POST -d '{"service":"Greeter","method":"SayHello","input":{"equals":{"name":"gripmock"}},"output":{"data":{"message":"Hello GripMock"}}}' 192.168.41.131:4771/add
```
## 使用grpc调试工具（bloomrpc）请求
参数：
```json
{
  "name": "gripmock"
}
```