---
title: "简介"
date: 2000-03-05T01:52:45+08:00
keywords: ["go-zeus"]
description: "go-zeus是一个使用golang技术栈开发的微服务框架（神王框架）、一个0依赖可插拔的微服务框架..."
categories: []
tags: ["go-zeus"]
weight: 1
---
## 介绍
go-zeus是一个使用golang技术栈开发的微服务框架（神王框架）、一个0依赖可插拔的微服务框架；基于微服务架构的抽象，其实现在zeus-plusin项目中，开发者可根据自己的技术栈进行灵活配置

go-zeus原生支持流量染色及泳道技术，很方便构建单元化架构，多云多活的系统等，这也是市面上的框架所没有的能力。

项目地址：[https://github.com/go-zeus/zeus](https://github.com/go-zeus/zeus)

文档地址：[https://doc.cncf.vip/go-zeus](https://doc.cncf.vip/go-zeus)

## 目录结构
```bash
├── README.md
├── app
├── balancer
├── client
├── components
├── configs
├── context
├── encoding
├── engine.go
├── examples
├── go.mod
├── log
├── metadata
├── registry
├── safe
├── server
├── service
├── types
└── utils
```

## 为什么需要该项目
本人主要从事云原生中间件相关的工作，所做的产品需要产品化才能让更多的用户使用，从而产生更大的价值。
### 为什么要造轮子？
网上徽服务框架那么多，为什么还要做一个？程序员圈子里流行这么一句话：“不要重复造轮子”。

首先我们要搞清楚两个概念 —— 造轮子和发明轮子。轮子是在距今6800年前被发明出来，在此之前我们的祖先可能已经就在尝试对轮子的创造。从三角形、四边形、五边形等等，一直到最后的圆形轮子，每种轮子都不一样，这叫做发明轮子。

而当轮子的形状确定后，再发明其它形状的轮子就没有意义了，改进轮子才是重点，于是就有了石轮、木轮、空心木轮、气轮的转变。而现在，气轮也有多种款式，适用于不同的场景，这叫做造轮子。

可以看出，发明轮子已经变得毫无意义，但是造轮子依然是人们所关注的焦点。

所以：

1.本项目不是重复造轮子，是创造产品需要的好轮子。

2.创造一个简单好用的微服务框架。
