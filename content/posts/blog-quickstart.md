---
title: "用hugo快速创建一个博客"
date: 2023-03-04T17:53:26+08:00
keywords: ["hugo","博客"]
description: "hugo是一款开源的博客生成软件"
categories: ["技术应用"]
tags : ["hugo","博客"]
thumbnail : "images/hugo_logo.jpg"
banner : "images/hugo_logo.jpg"
---
## 背景
博客是技术人员日常记录的一个载体，开源的博客系统有很多，我最终选择了hugo。
## 安装hugo
直接看官方文档：https://gohugo.io/installation/
## 创建博客
### 命令
```bash
hugo new site rushui.net -f yaml 
cd rushui.net
git init
git submodule add https://github.com/hugo-theme/rs.git themes/rs
echo "theme: rs" >> config.yaml
hugo server
```
### 保留目录
blog中所有目录都是空的，所以只保留用到的目录和文件即可
```bash
content #内容目录
static #静态文件目录
themes #主题目录
config.yaml #配置文件
```
### 创建内容
```bash
hugo new posts/blog-quickstart.md
```
posts/blog-quickstart.md 加入以下内容
```bash
---
title: "用hugo快速创建一个博客"
date: 2023-03-04T17:53:26+08:00
tags : ["如水"]
---
一个简单的go程序
```go
package main
import "fmt"
func main(){
  fmt.Println("hello world")
}
``` #去除后面空格
```
### 启动服务
```bash
hugo server -D
```
打开 http://localhost:1313/ 即可看到刚才加的内容