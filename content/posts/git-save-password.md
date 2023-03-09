---
title: "如何在Git中保存用户名和密码？"
date: 2023-03-09T23:24:33+08:00
keywords: ["git"]
description: ""
categories: ["技术应用"]
tags: ["git"]
thumbnail: ""
banner: ""
---
在装有git的机器上运行命令：
 ```bash
git config --global credential.helper store
```
然后git pull提供用户名和密码
输出用户名和密码后再次git pull就不会提示输入用户名和密码了。
查看保存的账号
```bash
cat ~/.git-credentials
```
如果不保存，直接把相关账号删除即可