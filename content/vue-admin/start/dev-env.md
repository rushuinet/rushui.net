---
title: "开发环境"
date: 2000-03-05T01:52:45+08:00
keywords: "vue"
description: "vue-admin开发所需的环境配置"
categories: []
tags: ["vue"]
weight: 1
---

## 开发工具

vscode

## 格式化工具

prettier

vscode 安装 prettier 插件 并设置默认格式化插件为默认值

设置方法：右击文档`使用...格式化文档`选择 prettier 并设置成默认（多个文档扩展名都要设置一下）

根目录下创建 .prettierrc

```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "none"
}
```

.prettierrc 文件常见配置：[https://www.rushui.net/posts/prettierrc-config/](https://www.rushui.net/posts/prettierrc-config/)

## vite
