---
title: "初始化"
date: 2000-03-05T01:52:45+08:00
keywords: ["vue"]
description: ""
categories: []
tags: ["vue"]
weight: 2
---

## 工具版本

- nodejs:v18.14.2
- npm:9.5.0

## 创建 vue-admin 项目

### vue 项目 create

```bash
pnpm create vue@latest
 Project name: ... demo
? Add TypeScript? » No / Yes
√ Add TypeScript? ... No / Yes
? Add JSX Support? » No / Yes
√ Add JSX Support? ... No / Yes
? Add Vue Router for Single Page Application development? » No / Yes
√ Add Vue Router for Single Page Application development? ... No / Yes
? Add Pinia for state management? » No / Yes
√ Add Pinia for state management? ... No / Yes
? Add Vitest for Unit Testing? » No / Yes
√ Add Vitest for Unit Testing? ... No / Yes
? Add an End-to-End Testing Solution? » - Use arrow-keys. Return to submit.
>   No
    Cypress
    Playwright

√ Add an End-to-End Testing Solution? » No
? Add ESLint for code quality? » No / Yes
√ Add ESLint for code quality? ... No / Yes

cd vue-admin
pnpm i
pnpm dev

```

这里安装的组件有 TypeScript、JSX、Vue Router、Pinia
打开 url http://localhost:5173/ 即可看到效果

### 配置 vite 文件更新监听

```vim
export default defineConfig({
  server: {
    watch: {usePolling:true},
    hmr:true
  },
  plugins: [
    vue(), vueJsx(),
  ],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  }
})
```

### 安装 element-plus

```
npm install element-plus --save
```

导入文档：https://element-plus.org/zh-CN/guide/quickstart.html

配置 Volar 支持
配置按需导入

### 黑暗模式

https://element-plus.org/zh-CN/guide/dark-mode.html

```vue
// main.ts // 如果只想导入css变量 import
'element-plus/theme-chalk/dark/css-vars.css'
```

### 引入 icon

https://element-plus.org/zh-CN/component/icon.html
在这里遇到了 Object.entries 方法不存在的情况需要在 tsconfig.json 的 compilerOptions 字段中增加

```json
{
  "compilerOptions": {
    ...
    "lib": [
      "esnext",
      "dom"
    ],
    ...
  },
}
```

tsconfig.json 配置详解参考:https://rushui.net/posts/tsconfig-json/

## LOGO

```svg
  <svg class="icon" width="200px" height="200.00px" viewBox="0 0 1024 1024" version="1.1"
      xmlns="http://www.w3.org/2000/svg">
      <path d="M0 0h1024v1024H0V0z" fill="#202425" opacity=".01" />
      <path
          d="M743.867733 102.4a17.066667 17.066667 0 0 1 12.049067 4.983467l256.750933 256.750933a17.066667 17.066667 0 0 1 0.7168 23.3472L524.8 941.226667a17.066667 17.066667 0 0 1-25.6 0L10.615467 387.4816a17.066667 17.066667 0 0 1 0.7168-23.3472l256.750933-256.750933A17.066667 17.066667 0 0 1 280.132267 102.4h463.735466z"
          fill="#11AA66" />
      <path
          d="M499.165867 360.789333L278.016 108.066133A3.413333 3.413333 0 0 1 280.576 102.4h462.848a3.413333 3.413333 0 0 1 2.56 5.666133l-221.149867 252.7232a17.066667 17.066667 0 0 1-25.668266 0z"
          fill="#FFAA44" />
      <path
          d="M250.606933 383.8976a34.133333 34.133333 0 0 1 48.128 3.242667L512 630.852267l213.230933-243.712a34.133333 34.133333 0 0 1 51.4048 44.919466l-238.933333 273.066667a34.133333 34.133333 0 0 1-51.4048 0l-238.933333-273.066667a34.133333 34.133333 0 0 1 3.242666-48.128z"
          fill="#FFFFFF" />
  </svg>
```

## 框架与样式

### 初始化样式

保留`src\assets\main.css`文件，其余删除，并增加以下初始样式

```css
body {
  font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB",
    "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0;
  padding: 0;
  height: 100%;
}

a {
  color: var(--ep-color-primary);
  text-decoration: none;
}

:root {
  --el-menu-item-height: 48px;
  --el-header-height: initial;
}

code {
  border-radius: 2px;
  padding: 2px 4px;
  background-color: var(--ep-color-primary-light-9);
  color: var(--ep-color-primary);
}
```

### 模板 layout

修改或新增的相关文件为：

- src/App.vue
- src/layouts/Sidecar.vue
- src/layouts/Header.vue
- src/layouts/Footer.vue
- src/layouts/TagView.vue

### App.vue

```vue
<template>
  <el-container>
    <Sicecar />
    <el-container>
      <el-header>
        <Header />
      </el-header>
      <el-main>
        <RouterView></RouterView>
      </el-main>
      <Footer />
    </el-container>
  </el-container>
</template>

<script setup lang="ts">
import Sicecar from "./layouts/Sidecar.vue";
import Header from "./layouts/Header.vue";
import Footer from "./layouts/Footer.vue";
</script>
```

### Sidecar.vue

```vue
<template>
  <el-aside :class="collapse.collapse ? 'el-aside--collapse' : ''">
    <el-menu
      default-active="2"
      class="el-menu-aside"
      :collapse="collapse.collapse"
      :router="true"
      @open="handleOpen"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="#ffd04b"
      @close="handleClose"
    >
      <div class="logo">
        <el-icon>
          <IconLogo />
        </el-icon>
        <span v-if="!collapse.collapse"> 后台管理系统 </span>
      </div>
      <el-menu-item index="/">
        <el-icon><icon-menu /></el-icon>
        <template #title> 控制台 </template>
      </el-menu-item>
      <el-menu-item index="/about">
        <el-icon>
          <document />
        </el-icon>
        <template #title> 文档列表 </template>
      </el-menu-item>
      <el-sub-menu index="3">
        <template #title>
          <el-icon>
            <location />
          </el-icon>
          <span>用户中心</span>
        </template>
        <el-menu-item-group>
          <template #title><span>用户</span></template>
          <el-menu-item index="1-1">用户列表</el-menu-item>
          <el-menu-item index="1-2">用户画像</el-menu-item>
        </el-menu-item-group>
        <el-menu-item-group title="商家">
          <el-menu-item index="1-3">商家列表</el-menu-item>
        </el-menu-item-group>
        <el-sub-menu index="1-4">
          <template #title><span>管理员</span></template>
          <el-menu-item index="1-4-1">管理员列表</el-menu-item>
        </el-sub-menu>
      </el-sub-menu>
      <el-menu-item index="4">
        <el-icon>
          <setting />
        </el-icon>
        <template #title>系统设置</template>
      </el-menu-item>
    </el-menu>
  </el-aside>
</template>

<script lang="ts" setup>
import { useCollapseStore } from "@/stores/collapse";
import {
  Document,
  Menu as IconMenu,
  Location,
  Setting,
} from "@element-plus/icons-vue";
import IconLogo from "../components/icons/iconLogo.vue";
const collapse = useCollapseStore();
const handleOpen = (key: string, keyPath: string[]) => {
  console.log(key, keyPath);
};
const handleClose = (key: string, keyPath: string[]) => {
  console.log(key, keyPath);
};
</script>

<style scoped>
.logo {
  color: #dcdfe6;
  text-align: center;
  height: 48px;
  border-bottom: 0px solid #dcdfe6;
}

.logo .el-icon {
  font-size: 26px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.logo span {
  margin-left: 10px;
  display: inline-block;
  line-height: 30px;
  vertical-align: super;
}

.el-menu-aside {
  height: 100%;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  overflow: hidden auto;
}

.el-aside {
  width: var(--ep-aside-width, 200px);
}

.el-aside--collapse {
  width: var(--ep-aside-width, 65px);
}
</style>
```

### Header.vue

```vue
<template>
  <el-header>
    <el-menu
      :default-active="activeIndex"
      class="el-menu-header"
      mode="horizontal"
      :ellipsis="false"
      @select="handleSelect"
    >
      <div class="el-collapse-icon">
        <a @click="collapse.change()">
          <el-icon v-if="collapse.collapse">
            <Expand />
          </el-icon>
          <el-icon v-else>
            <Fold />
          </el-icon>
        </a>
      </div>
      <div class="flex-grow" />
      <el-menu-item index="1">
        <RouterLink to="/">首页</RouterLink>
      </el-menu-item>
      <el-menu-item index="2">
        <RouterLink to="/about">关于</RouterLink>
      </el-menu-item>
      <div class="dark-icon" @click="toggleDark()">
        <el-icon>
          <Moon v-if="isDark" />
          <Sunny v-else />
        </el-icon>
      </div>
      <el-sub-menu index="100">
        <template #title>
          <el-icon>
            <Avatar />
          </el-icon>
          管理员
        </template>
        <el-menu-item index="100-1">个人中心</el-menu-item>
        <el-menu-item index="100-2">退出</el-menu-item>
      </el-sub-menu>
    </el-menu>
  </el-header>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import { Expand, Fold, Moon, Sunny, Avatar } from "@element-plus/icons-vue";
import { useCollapseStore } from "@/stores/collapse";
import { toggleDark, isDark } from "@/stores/dark";
const collapse = useCollapseStore();
const activeIndex = ref("1");
const handleSelect = (key: string, keyPath: string[]) => {
  console.log(key, keyPath);
};
</script>

<style>
.el-header {
  --el-header-padding: 0;
  --el-header-height: initial;
}

.el-menu-header a {
  font-size: 14px;
  font-weight: 500;
}

.el-collapse-icon a {
  margin: 15px;
  font-size: 20px;
  line-height: 50px;
}

.dark-icon {
  font-size: 20px;
  margin-top: 15px;
  cursor: pointer;
}

.flex-grow {
  flex-grow: 1;
}
</style>
```

### Footer.vue

```vue
<template>
  <el-footer>
    <div>©2023</div>
  </el-footer>
</template>

<style scoped>
.el-footer {
  line-height: 48px;
  vertical-align: middle;
  height: 48px;
  border-top: 1px solid #dcdfe6;
}
</style>
```
