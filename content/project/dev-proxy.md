---
title: "开发联调神器dev-proxy"
date: 2023-03-08T23:45:55+08:00
keywords: ["开发联调神器"]
description: "开发联调神器是一个http代理服务器，也可做为简单网关使用，设置简单，一个路由表设置可让流量打到内网设置的机器上，也支持外部IP和域名的代理。"
categories: ["开源项目"]
tags: ["开发联调神器"]
thumbnail: "images/dev_proxy_thumbnail.jpg"
banner: "images/dev_proxy.jpg"
---

## dev-proxy
开发联调神器是一个http代理服务器，也可做为简单网关使用，设置简单，一个路由表设置可让流量打到内网设置的机器上，也支持外部IP和域名的代理。
## 管理地址
http://IP:PORT/admin

## 设置示例
```bash
test=>192.168.8.8:8080
dev=>192.168.8.6:8888

假如服务域名为 dev.com 映射如下
dev.com/test/product/list => http://192.168.8.8:8080/product/list
dev.com/dev/order/list => http://192.168.8.6:8888/order/list

注意:
1.每行为一个映射
2.dev.com机器能访问 IP 192.168.8.8
3.目前只能代理http服务
```
## docker 
```bash
docker run -d -p 8888:80 gogateway/dev
```

## 使用方法
```go
package main

import (
	"flag"
	"github.com/go-proxy/dev"
	"log"
	"net/http"
	"os"
)

func main() {
	port := os.Getenv("port")
	if port == "" {
		p := flag.String("port", "8888", "port default 8888")
		port = *p
	}
	dev := dev.NewProxy()
	log.Println("start port :" + port)
	err := http.ListenAndServe(":"+port, dev)
	if err != nil {
		log.Fatal(err)
	}
}
```

## 应用示例
dev-ingress 一个简单的k8s-ingress 

项目地址 ：https://github.com/gogateway/dev