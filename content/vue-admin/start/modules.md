---
title: "模块"
date: 2000-03-05T01:52:45+08:00
keywords: ["vue"]
description: ""
categories: []
tags: ["vue"]
weight: 5
---

## 面包屑

`src/layout/components/Breadcrumb.vue`

```vue
<template>
  <el-breadcrumb separator="/">
    <el-breadcrumb-item
      v-for="item in routers"
      :key="item.path"
      :to="{ path: item?.path }"
      >{{ item?.meta?.title || "--" }}</el-breadcrumb-item
    >
  </el-breadcrumb>
</template>
<script setup lang="ts">
import { useRouter } from "vue-router";
import { computed } from "vue";
// 处理点击事件
const router = useRouter();
// 当前路由的匹配记录
console.log(router.currentRoute.value.matched);
const routers = computed(() => {
  // 过滤掉没有meta的
  return router.currentRoute.value.matched.filter(
    (item) => item.meta.title || null
  );
});
</script>
```

## TagsView

```


```
