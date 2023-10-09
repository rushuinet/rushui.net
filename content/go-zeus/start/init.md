---
title: "初始化"
date: 2000-03-05T01:52:45+08:00
keywords: ["go-zeus"]
description: ""
categories: []
tags: ["go-zeus"]
weight: 1
---

## 安装
```bash
go get -u github.com/go-zeus/zeus
```

## 示例
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