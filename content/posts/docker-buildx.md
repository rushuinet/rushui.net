---
title: "Docker使用Buildx构建多平台镜像"
date: 2023-06-05T13:44:45+08:00
keywords: ["docker"]
description: "buildx 是 Docker 官方提供的一个构建工具，它可以帮助用户快速、高效地构建 Docker 镜像，并支持多种平台的构建。使用 buildx，用户可以在单个命令中构建多种架构的镜像，例如 x86 和 ARM 架构，而无需手动操作多个构建命令。"
categories: ["技术应用"]
tags: ["docker"]
thumbnail: ""
banner: ""
---
## 起因
最近使用M1时发现构建的镜像与云服务的架构不符导致云服务器的镜像跑不起来，出现下面错误，最好的解决方案是使用buildx一次性构建多平台镜像。
```bash
WARNING: The requested image's platform (linux/arm64/v8) does not match the detected host platform (linux/amd64) and no specific platform was requested
standard_init_linux.go:228: exec user process caused: exec format error
```
## 简介
`buildx` 是 `Docker` 官方提供的一个构建工具，它可以帮助用户快速、高效地构建 `Docker` 镜像，并支持多种平台的构建。使用 `buildx`，用户可以在单个命令中构建多种架构的镜像，例如 x86 和 ARM 架构，而无需手动操作多个构建命令。此外，`buildx` 还支持 Dockerfile 的多阶段构建和缓存，这可以大大提高镜像构建的效率和速度。要安装并使用 `buildx`，需要 Docker Engine 版本号大于等于 19.03。如果你使用的是 Docker Desktop，则默认安装了 `buildx`。可以使用 `docker buildx version` 命令查看安装版本，得到以下类似输出，证明已经安装过了。
```bash
docker buildx version
github.com/docker/buildx v0.10.4 c513d34049e499c53468deac6c4267ee72948f02
```
如需要安装，查看官方文档：
https://docs.docker.com/build/architecture/

## 命令
```bash
# 查看当前的builder实例
docker buildx ls
# 创建新的builder实例
docker buildx create --name [NAME]
# 切换到指定的builder实例
docker buildx use [NAME]
# 检查当前builder实例确保该实例已经启动
docker buildx inspect [NAME] --bootstrap
# 停止builder实例
docker buildx stop [NAME]
# 删除builder实例
docker buildx rm [NAME]
```

## 创建Buildx构建容器
```bash
#创建并使用buildx构建容器
docker buildx create --use --name arm_builder
#初始化构建容器
docker buildx inspect --bootstrap

Name:          arm_builder
Driver:        docker-container
Last Activity: 2023-06-05 19:58:39 +0000 UTC

Nodes:
Name:      arm_builder0
Endpoint:  unix:///var/run/docker.sock
Status:    running
Buildkit:  v0.11.6
Platforms: linux/arm64, linux/amd64, linux/amd64/v2, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/mips64le, linux/mips64, linux/arm/v7, linux/arm/v6
```
当初始化成功，会拉下来moby/buildkit:buildx-stable-1的镜像，并启动构建容器。通过 docker ps 查看如下：
```bash
docker ps
CONTAINER ID   IMAGE                           COMMAND               CREATED       STATUS       PORTS                 NAMES
8c64cc3388eb   moby/buildkit:buildx-stable-1   "buildkitd"           3 hours ago   Up 3 hours                         buildx_buildkit_arm_builder0
```
## 构建不同平台镜像
这里使用了`amd64`、`arm64`,amd64=x86_64=x64!=IA64,arm64用于苹果芯片
```bash
docker buildx build --platform linux/amd64,linux/arm64 --no-cache --push -t ${image} .
```
## buildx构建本地镜像
这里使用了`--load`参数，并且`--platform` 只能有一个参数，`linux/arm64` 根据自己的系统选择
```bash
docker buildx build --platform linux/arm64 --no-cache --load -t ${image} .
```
## 项目使用示例
https://github.com/basicimage/alpine