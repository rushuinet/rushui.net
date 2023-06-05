---
title: "mac下docker命令自动补齐"
date: 2023-04-24T20:29:53+08:00
keywords: ["docker"]
description: ""
categories: ["技术文档"]
tags: ["docker"]
thumbnail: ""
banner: ""
---
## 1. 首先得安装了homebrew，如果没装，先去装一下吧；
[Mac安装Homebrew](https://www.rushui.net/posts/mac-install-homebrew/)
## 2. 安装bash-complerion
```bash
brew install bash-completion
```
我的安装的默认补齐脚本在`/opt/homebrew/etc/bash_completion.d`目录下，有的在`/usr/local/etc/bash_completion.d/`下，这个看系统。
## 3. 在~/.bash_profile中加入以下内容
```bash
[[ -r "/opt/homebrew/etc/profile.d/bash_completion.sh" ]] && . "/opt/homebrew/etc/profile.d/bash_completion.sh"
```
记得`source ~/.bash_profile`，或者新打开命令窗口
## 4. 建立docker补齐脚本的软链接
```bash
cd /opt/homebrew/etc/bash_completion.d
ln -s /Applications/Docker.app/Contents/Resources/etc/docker.bash-completion docker
ln -s /Applications/Docker.app/Contents/Resources/etc/docker-compose.bash-completion docker-compose
```
重启终端即可

## ps
centos默认补齐目录`/usr/share/bash-completion/completions/`