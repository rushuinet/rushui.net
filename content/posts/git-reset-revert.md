---
title: "git reset与git revert"
date: 2023-04-03T22:51:43+08:00
keywords: ["git"]
description: ""
categories: ["技术应用"]
tags: ["git"]
thumbnail: ""
banner: ""
---

### reset用法
```
# HEAD^表示上一次操作，也可以是commit操作的哈希ID
git reset --hard HEAD^ (全部撤消，会删除改动)
git reset --soft HEAD^  （撤回到暂存区，注意会再次推送到远程）
git reset --mixed HEAD^ （撤回到工作区，平时用这个）

# 推送到远程
git push origin HEAD --force
```

### revert 用法(反做命令)
```
git revert -n revert时生成的版本号
```