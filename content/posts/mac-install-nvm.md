---
title: "Mac安装nvm"
date: 2024-01-11T22:29:34+08:00
keywords: ["nvm"]
description: "VM 是一个Node.js版本管理工具，可以让我们轻松地在不同的Node.js版本之间切换。 它可以在全局和本地范围内安装多个版本的Node.js，并可以在它们之间切换。 此外，NVM 还可以让我们安装和使用不同的npm版本。"
categories: ["技术文档"]
tags: ["nvm"]
thumbnail: ""
banner: ""
---
## 介绍
VM 是一个Node.js版本管理工具，可以让我们轻松地在不同的Node.js版本之间切换。 它可以在全局和本地范围内安装多个版本的Node.js，并可以在它们之间切换。 此外，NVM 还可以让我们安装和使用不同的npm版本。
## 安装
```bash
git clone https://github.com/nvm-sh/nvm.git
cd nvm
sh install.sh
```
## 配置环境变量
```bash
vim ~/.bash_profile
# 在结尾加入以下内容
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

# 使配置生效
source ~/.bash_profile
```
完成后就可以使用nvm -v查看版本了。
## 使用
```bash
nvm use 18  # 如果没有会提示 nvm install 18
```
## 常用操作指令
```bash
# 列出远程版本
nvm ls-remote
# 列出所有版本
nvm ls
# 安装指定版本node
vm install [version]
# 切换制定的node版本
nvm use [version]
# 显示当前版本
nvm current
# 解除nvm指定的版本，返回系统使用的
nvm deactivate
# 给不同的版本号添加别名
nvm alias <name> <version>
# 卸载指定的版本
nvm uninstall <version>
# 从shell中卸载nvm
nvm unload	
```