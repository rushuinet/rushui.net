---
title: "git clone --depth=1使用场景和用法"
date: 2023-03-11T23:41:31+08:00
keywords: ["git"]
description: "一般仓库文件不大时，我们都可以用这个方法git clone仓库，但问题是有时候，在仓库历史的某次commit时，有人不小心提交了1G的文件..."
categories: ["技术应用"]
tags: ["git"]
thumbnail: ""
banner: ""
---

使用场景：
一般仓库文件不大时，我们都可以用这个方法git clone仓库，但问题是有时候，在仓库历史的某次commit时，有人不小心提交了1G的文件，虽然后面的commit中他把这个文件删除了，但是在.git文件夹中仍然存储着这个文件，所以如果我们克隆仓库这个仓库，会把所有的历史协作记录都clone下来，这样整个文件会非常大，其实对于我们直接使用仓库，而不是参与仓库工作的人来说，只要把最近的一次commit给clone下来就好了。

这就好比一个产品有很多个版本，我们只要clone最近的一个版本来使用就行了。实现这个功能就需要用到git clone --depth=1命令。

用 git clone --depth=1 的好处是限制 clone 的深度，不会下载 Git 协作的历史记录，这样可以大大加快克隆的速度

适合用 git clone --depth=1 的场景：你只是想clone最新版本来使用或学习，而不是参与整个项目的开发工作。