---
title: "示例"
date: 2000-03-05T01:52:45+08:00
keywords: ["go-zeus"]
description: ""
categories: []
tags: ["go-zeus"]
weight: 2
---
## app示例
```go
package main

import (
	"github.com/go-zeus/zeus/app"
	"github.com/go-zeus/zeus/log"
)

func main() {
	log.Fatal(app.New().Run(make(chan struct{})))
}

// curl http://localhost:8080
```
## 使用原生路由示例
```go
package main

import (
	"github.com/go-zeus/zeus/log"
	"github.com/go-zeus/zeus/server"
	"github.com/go-zeus/zeus/service"
	"net/http"
)

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/hi", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("test server"))
	})
	ser := server.New(server.Mux(mux))

	done := make(<-chan struct{})
	log.Fatal(service.New(service.Server(ser)).Run(done))
}

// curl http://localhost:8080/hi
```