---
title: "关于文集"
date: 2000-03-05T01:52:45+08:00
keywords: "关于文集"
description: "文集版块类似文档化风格展现的页面风格，是一类文档的集合"
categories: []
tags: ["文集"]
weight: 1
---

文集版块类似文档化风格展现的页面风格，是一类文档的集合。

## 版式介绍
内容左侧是文档列表（filetree），中间内容区，右侧为内容列表（toc）
### 文档列表（filetree）
凡在content/docs下的文档，都可以以目录的形式展示在这里，其中顶级目录的文档显示为一级会链接到文档内容；
一级目录以分组形式展示，无链接。原则上文档可以无限深度，但最好深度最多3级。
### 内容区
内容区是显示页面内容的地方，以Markdown语法书写。
### 内容列表（toc）
内容列表是当前页面以Markdown标记 # ## ### 三级 组织的，可配置。
## 风格介绍
### 仓库地址
- 目前：https://github.com/hugo-theme/rs
- 以后：文集会独立成文档主题，可做为开源项目文档，书籍等：https://github.com/hugo-theme/docs
### 技术栈
jQuery v3.5.1 + Bootstrap v4.6.0 + HTML5
## 文档排序
文档属性有 `weight: 2` 字段，数字越小越靠前，如果一个目录下有文档也有目录，目录取 `dir/_index.md` 中的 `weight` 字段与文档在同一级排序