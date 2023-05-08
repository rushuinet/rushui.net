---
title: "vite vue3 中如何在 js 中使用 scss 变量"
date: 2023-04-14T23:52:31+08:00
keywords: ["vite", "vue3", "scss"]
description: "不管工作还是学习，我都很喜欢用 scss 和 less ，真的比纯 css 方便太多了。本文讲解如何在 js 里导入 scss 的变量。"
categories: ["技术文档"]
tags: ["vite", "vue3", "scss"]
thumbnail: ""
banner: ""
---

## vite vue3 中如何在 js 中使用 scss 变量

不管工作还是学习，我都很喜欢用 scss 和 less ，真的比纯 css 方便太多了。

本文讲解如何在 js 里导入 scss 的变量。

在动态换肤的网站里这种做法很常见。

我使用 vite 搭建一个 vue3 项目来举例。

## 搭建项目

```bash
pnpm create vite
```

项目名：vitescss

选择 vue 和 TypeScript

```bash
cd vitescss
pnpm install
pnpm install sass
```

注意，安装的是 sass 。但我们是可以使用 scss 语法的。

修改 App.vue 为如下代码 `npm run dev` 即可查看效果

```vue
<template>
  <div class="demo">
    <button
      v-for="(item, index) in btns"
      :key="index"
      @click="onBtnClick(item.bgColor, item.textColor)"
    >
      {{ item.title }}
    </button>

    <div>
      <div class="example" ref="exampleRef">Hello World</div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from "vue";

const btns = [
  { title: "红色主题", bgColor: "#FF9191", textColor: "#FF0000" },
  { title: "蓝色主题", bgColor: "#B3C4FF", textColor: "#042BA9" },
  { title: "默认主题", bgColor: "#333333", textColor: "#FFFFFF" },
];
const currentBgColor = ref("#333333");
const currentTextColor = ref("#FFFFFF");
const onBtnClick = (bgColor: string, textColor: string) => {
  console.log(bgColor, textColor);
  currentBgColor.value = bgColor;
  currentTextColor.value = textColor;
};
</script>

<style scoped lang="scss">
.demo {
  padding: 10px;

  .example {
    --textColor: v-bind(currentTextColor);
    --bgColor: v-bind(currentBgColor);

    display: inline-block;
    margin-top: 20px;
    font-size: 20px;
    padding: 20px 50px;
    color: var(--textColor);
    background: var(--bgColor);
  }
}
</style>
```
