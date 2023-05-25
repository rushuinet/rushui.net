---
title: "解决GoLand无法Debug"
date: 2023-05-25T18:03:10+08:00
keywords: ["goland","debug"]
description: ""
categories: ["技术应用"]
tags: ["goland","debug"]
thumbnail: ""
banner: ""
---
## Goland 启动的时候出现这个警告
undefined behavior - version of Delve is too old for Go version 1.20.3 (maximum supported version 1.18)

出现这个错误后，说明是调试工具`dlv`版本太低的原因，升级即可
## 安装dlv
```bash
go install github.com/go-delve/delve/cmd/dlv@latest
whereis dlv #查看dlv安装位置 一般是 $GOPATH/bin下
$GOPATH/bin/dlv
```
启动goland的debug模式，找到goland中的dlv目录，我的是 `/Applications/GoLand.app/Contents/plugins/go/lib/dlv/macarm/dlv`
## cp到goland默认目录中
```bash
rm /Applications/GoLand.app/Contents/plugins/go/lib/dlv/macarm/dlv
cp $GOPATH/bin/dlv /Applications/GoLand.app/Contents/plugins/go/lib/dlv/macarm/
```
也可以在goland->帮助->自定义属性添加`dlv.path=$GOPATH/bin/dlv`解决。