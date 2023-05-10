---
title: "Go语言 http 中的 request.Host 和 request.URL.Host 的区别"
date: 2023-05-10T19:21:37+08:00
keywords: ["go","http","request"]
description: ""
categories: ["技术文档"]
tags: ["go","http","request"]
thumbnail: ""
banner: ""
---

## golang 中的例子
http 代码：
```go
http.ListenAndServe("localhost:9999", nil)
```
使用 `curl` 命令访问：`curl http://localhost:9999/_geecache/scores/Tom`，则：
```bash
r.Host 是 localhost:9999，
r.URL.Host 是空字符串，
r.URL.Path 是 /_geecache/scores/Tom。
# 定义
r.URL 字段是通过解析 HTTP 请求 URI 创建的。
r.Host 字段是主机请求标头的值。它与调用 r.Header.Get("Host") 的值相同。
```
如果网上的HTTP请求是：
```bash
GET /pub/WWW/TheProject.html HTTP/1.1
Host: www.example.org:8080
则：r.URL.Host 是空字符串，
r.Host 是 www.example.org:8080。
```
解释
```bash
r.URL.Host 和 r.Host的值几乎总是不同的。在代理服务器上，r.URL.Host 是目标服务器的主机，r.Host 是代理服务器本身的主机。当不通过代理连接时，客户端不会在请求 URI 中指定主机。在这种情况下，r.URL.Host 是空字符串。
```
如果未实现代理，则应使用r.Host确定主机。

