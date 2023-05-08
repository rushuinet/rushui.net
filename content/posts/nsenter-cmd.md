---
title: "nsenter命令简介"
date: 2023-04-18T00:22:26+08:00
keywords: ["nsenter"]
description: "nsenter命令是一个可以在指定进程的命令空间下运行指定程序的命令。它位于util-linux包中。"
categories: ["技术文档"]
tags: ["nsenter"]
thumbnail: ""
banner: ""
---

## 介绍

nsenter 命令是一个可以在指定进程的命令空间下运行指定程序的命令。它位于 util-linux 包中。

## nsenter 命令的语法：

```bash
nsenter [options] [program [arguments]]

options:
-t, --target pid：指定被进入命名空间的目标进程的pid
-m, --mount[=file]：进入mount命令空间。如果指定了file，则进入file的命令空间
-u, --uts[=file]：进入uts命令空间。如果指定了file，则进入file的命令空间
-i, --ipc[=file]：进入ipc命令空间。如果指定了file，则进入file的命令空间
-n, --net[=file]：进入net命令空间。如果指定了file，则进入file的命令空间
-p, --pid[=file]：进入pid命令空间。如果指定了file，则进入file的命令空间
-U, --user[=file]：进入user命令空间。如果指定了file，则进入file的命令空间
-G, --setgid gid：设置运行程序的gid
-S, --setuid uid：设置运行程序的uid
-r, --root[=directory]：设置根目录
-w, --wd[=directory]：设置工作目录

如果没有给出program，则默认执行$SHELL
```

## 示例：

运行一个 nginx 容器，查看该容器的 pid：

```bash
docker inspect -f {{.State.Pid}} nginx
54325
# 进入net命令空间
nsenter -n -t54325
ip addr
# 进入容器nsenter -m -u -i -n -p -t
nsenter --target 54325 --mount --uts --ipc --net --pid
root@164f44ff58d1:/bin#
```

在 Kubernetes 中，在得到容器 pid 之前还需获取容器的 ID，可以使用如下命令获取：

```bash
kubectl get pod test -oyaml|grep containerID
  - containerID: docker://cf0873782d587dbca6aa32f49605229da3748600a9926e85b36916141597ec85
```

或者更为精确地获取 containerID：

```bash
kubectl get pod test -o template --template='{{range .status.containerStatuses}}{{.containerID}}{{end}}'
docker://cf0873782d587dbca6aa32f49605229da3748600a9926e85b36916141597ec85
```

## 原理

namespace 是 Linux 中一些进程的属性的作用域，使用命名空间，可以隔离不同的进程。

Linux 在不断的添加命名空间，目前有：

- mount：挂载命名空间，使进程有一个独立的挂载文件系统，始于 Linux 2.4.19
- ipc：ipc 命名空间，使进程有一个独立的 ipc，包括消息队列，共享内存和信号量，始于 Linux 2.6.19
- uts：uts 命名空间，使进程有一个独立的 hostname 和 domainname，始于 Linux 2.6.19
- net：network 命令空间，使进程有一个独立的网络栈，始于 Linux 2.6.24
- pid：pid 命名空间，使进程有一个独立的 pid 空间，始于 Linux 2.6.24
- user：user 命名空间，是进程有一个独立的 user 空间，始于 Linux 2.6.23，结束于 Linux 3.8
- cgroup：cgroup 命名空间，使进程有一个独立的 cgroup 控制组，始于 Linux 4.6

Linux 的每个进程都具有命名空间，可以在/proc/PID/ns 目录中看到命名空间的文件描述符。

```bash
pwd
/proc/1/ns
ll
total 0
lrwxrwxrwx 1 root root 0 Sep 23 19:53 ipc -> ipc:[4026531839]
lrwxrwxrwx 1 root root 0 Sep 23 19:53 mnt -> mnt:[4026531840]
lrwxrwxrwx 1 root root 0 Sep 23 19:53 net -> net:[4026531956]
lrwxrwxrwx 1 root root 0 Sep 23 19:53 pid -> pid:[4026531836]
lrwxrwxrwx 1 root root 0 Sep 23 19:53 user -> user:[4026531837]
lrwxrwxrwx 1 root root 0 Sep 23 19:53 uts -> uts:[4026531838]
```
