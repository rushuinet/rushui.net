---
title: "npm、yarn和pnpm 设置淘宝镜像"
date: 2023-04-14T23:50:17+08:00
keywords: ["npm", "pnpm", "yarn"]
description: ""
categories: ["技术应用"]
tags: ["npm", "pnpm", "yarn"]
thumbnail: ""
banner: ""
---

npm、yarn 和 pnpm 淘宝镜像

```bash
// 设置
npm config set registry https://registry.npmmirror.com/
yarn config set registry https://registry.npmmirror.com/
pnpm config set registry https://registry.npmmirror.com/
// 查看
npm config get registry
yarn config get registry
pnpm config get registry
```

node-sass 淘宝镜像设置

```bash
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/
yarn config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/
pnpm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/
```
