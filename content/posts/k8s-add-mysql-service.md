---
title: "k8s使用外部mysql做内部服务"
date: 2023-03-12T19:41:38+08:00
keywords: ["k8s","mysql"]
description: ""
categories: ["技术应用"]
tags: ["k8s","mysql"]
thumbnail: ""
banner: ""
---

### 创建 mysql-endpoints.yaml
```yaml
apiVersion: v1
kind: Endpoints
metadata:
  name: mysql-dev
  namespace: default
subsets:
  - addresses:
    - ip: 10.1.0.32
    ports:
      - port: 3306
```

### 创建mysql-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-dev
spec:
  ports:
    - port: 3306
    
```
或者
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-dev
  namespace: default
spec:
  clusterIP: 10.1.61.45
  ports:
  - port: 3306
    targetPort: 3306
    protocol: TCP
```

### 使用数据库服务
main.go
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
	_ = http.ListenAndServe(":8081", nil)
}
```

