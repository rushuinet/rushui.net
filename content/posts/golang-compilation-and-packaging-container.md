---
title: "golang 交差编译与打包容器"
date: 2023-03-06T23:00:18+08:00
keywords: ["golang","容器"]
description: ""
categories: ["技术应用"]
tags: ["golang","容器"]
thumbnail: ""
banner: ""
---

### build.sh
```bash
#!/usr/bin/env bash
CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o webdemo .
docker build -t rushui/webdemo .
```
### Dockerfile
```bash
FROM alpine:latest
COPY ./webdemo /usr/local/bin/webdemo
WORKDIR /usr/local/bin/
EXPOSE 8080
CMD [ "webdemo" ]
```

### webdome示例（go）
```go
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	"net/http"
)

func IndexHandler(w http.ResponseWriter, r *http.Request) {
	_, _ = fmt.Fprintln(w, "hello rushui")
}

func RowsHandler(w http.ResponseWriter, r *http.Request) {
	//第⼀步：打开数据库,格式是 ⽤户名：密码@/数据库名称？编码⽅式
	db, err := sql.Open("mysql", "root:root@tcp(mysql-dev:3306)/test?charset=utf8")
	if err != nil {
		fmt.Println(err)
	}
	//关闭数据库
	var id int
	var name string
	//查询数据，指定字段名，返回sql.Rows结果集
	rows, err := db.Query("select id,name from user")
	if err != nil {
		fmt.Println(err)
	}
	defer rows.Close()
	for rows.Next() {
		_ = rows.Scan(&id, &name)
		_, _ = fmt.Fprintln(w, name)
	}
}

func main() {
	http.HandleFunc("/", IndexHandler)
	http.HandleFunc("/rows", RowsHandler)
	_ = http.ListenAndServe(":8080", nil)
}
```