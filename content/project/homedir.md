---
title: "homedir 用于检测用户的主目录的Go库"
date: 2023-04-25T22:38:17+08:00
keywords: ["homedir"]
description: "用于检测用户的主目录的Go库，支持mac、linux、windows等多种操作系统，带将目录展开到home目录功能。"
categories: ["开源项目"]
tags: ["homedir"]
thumbnail: ""
banner: ""
---

## homedir
用于检测用户的主目录的Go库，支持mac、linux、windows等多种操作系统，带将目录展开到home目录功能。
## 用法
```bash
go get github.com/go-basic/homedir
```
用法非常简单，只需调用' homedir.Dir() '来获取用户主目录

使用' homedir.Expand() '将路径中的' ~ '展开到home目录中。

如： HOME=/custom/path/

homedir.Expand("~/foo/bar") 输出 /custom/path/foo/bar

## 示例
```go
package main

import (
	"fmt"
	"github.com/go-basic/homedir"
)

func main()  {
	dir,err := homedir.Dir()
	if err != nil {
		panic(err)
    }
	fmt.Println(dir)

	foo,_ := homedir.Expand("~/foo")
	fmt.Println(foo)
}
```