---
title: "Mac安装Homebrew"
date: 2023-04-24T19:20:20+08:00
keywords: ["Homebrew"]
description: ""
categories: ["技术文档"]
tags: ["Homebrew"]
thumbnail: ""
banner: ""
---
## 终端设置代理

```bash
export ALL_PROXY=socks5://127.0.0.1:1080
```
## 安装
官方首页：[https://brew.sh/index_zh-cn](https://brew.sh/index_zh-cn)

只需一个命令
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
测试 Homebrew 是否正确安装
```bash
brew -h
```
如果找不到命令`~/.bashrc`或`~/.bash_profile`加入下面内容再执行上面命令
```bash
sudo vim ~/.bashrc
export PATH="$PATH:/opt/homebrew/bin"
source ~/.bashrc
```
系统不同，安装目录也不同的，要看下`install.sh`脚本里的路径，我的是m1所以是`/opt/homebrew`
```bash
if [[ "${UNAME_MACHINE}" == "arm64" ]]
  then
    # On ARM macOS, this script installs to /opt/homebrew only
    HOMEBREW_PREFIX="/opt/homebrew"
    HOMEBREW_REPOSITORY="${HOMEBREW_PREFIX}"
  else
    # On Intel macOS, this script installs to /usr/local only
    HOMEBREW_PREFIX="/usr/local"
    HOMEBREW_REPOSITORY="${HOMEBREW_PREFIX}/Homebrew"
  fi
```

## 常用命令
```bash
基础命令
命令 描述
brew config 查看配置
brew -v 查看当前 homebrew 版本号
brew list 列出已安装的软件
brew search 关键字 根据关键字查找可安装的软件
brew outdated 查看可更新的软件
brew info softwareName 查看某个软件的详细信息
brew upgrade softwareName 升级某个软件的详细信息
brew install、brew uninstall 安装、卸载

brew install redis
brew install nginx
brew install php@7.2
// 卸载通过brew install安装的软件
brew uninstall redis
brew serivces 命令

brew serivces 命令是专门用来管理通过 homebrew 安装的各类服务的

// 查看所有服务及它们各自的运行状态
brew services list
// 启动服务
brew services start redis
// 停止服务
brew services stop redis
// 重启服务
brew services restart redis
```