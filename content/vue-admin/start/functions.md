---
title: "功能"
date: 2000-03-05T01:52:45+08:00
keywords: "vue"
description: ""
categories: []
tags: ["vue"]
weight: 5
---

## axios

用于与后端交互的组件

### 安装

```bash
pnpm i -S axios
```

### 封装业务公共请求

`apis/client/types.ts`

```ts
import type { AxiosResponse, InternalAxiosRequestConfig } from "axios";

// 业务接口结构
interface ApiResult<T = any> extends AxiosResponse {
  code: number;
  data: T;
  msg: string;
  error?: boolean;
  response?: AxiosResponse;
}

interface ApiRequestConfig extends InternalAxiosRequestConfig {
  // 用户自定义配置
  silent?: boolean;
}

export type { ApiResult, ApiRequestConfig };
```

### 封装公共请求拦截

`apis/client/service.ts`

```ts
import axios from "axios"; // 引入axios
import type { AxiosInstance, AxiosResponse, AxiosError } from "axios";
import type { ApiResult, ApiRequestConfig } from "./types";
import { ElMessage, ElMessageBox } from "element-plus";
import router from "@/router/index";

class Service {
  service: AxiosInstance;

  constructor(baseURL = "/", timeout = 30000) {
    this.service = axios.create({
      baseURL,
      timeout,
      headers: {
        "Content-type": "application/json",
      },
    });
    this.interceptors();
  }
  interceptors() {
    // http request 拦截器
    this.service.interceptors.request.use(
      (config: ApiRequestConfig) => {
        return config;
      },
      (error: AxiosError) => {
        ElMessage({
          showClose: true,
          message: error.message,
          type: "error",
        });
        return error;
      }
    );
    // http response 拦截器
    this.service.interceptors.response.use(
      (response: AxiosResponse) => {
        const resData = response.data as ApiResult;
        if (resData.code === 0) {
          return { data: resData.data, response } as any;
        } else {
          const { silent } = response.config as ApiRequestConfig;
          !silent &&
            ElMessage({
              showClose: true,
              message: response.data.msg || decodeURI(response.headers.msg),
              type: "error",
            });
          if (response.data.data && response.data.data.reload) {
            localStorage.clear();
            router.push({ name: "login", replace: true });
          }
          return { error: true, response };
        }
      },
      (error: AxiosError) => {
        if (!error.response) {
          ElMessageBox.confirm(
            `
              <p>检测到请求错误</p>
              <p>${error}</p>
              `,
            "请求报错",
            {
              dangerouslyUseHTMLString: true,
              distinguishCancelAndClose: true,
              confirmButtonText: "稍后重试",
              cancelButtonText: "取消",
            }
          ).then((r) => r.action.replace.name);
          return { error: true };
        }

        switch (error.response.status) {
          case 500:
            ElMessageBox.confirm(
              `
              <p>检测到接口错误${error}</p>
              <p>错误码<span style="color:red"> 500 </span>：此类错误内容常见于后台panic，请先查看后台日志，如果影响您正常使用可强制登出清理缓存</p>
              `,
              "接口报错",
              {
                dangerouslyUseHTMLString: true,
                distinguishCancelAndClose: true,
                confirmButtonText: "清理缓存",
                cancelButtonText: "取消",
              }
            ).then(() => {
              router.push({ name: "login", replace: true });
            });
            break;
          case 404:
            ElMessageBox.confirm(
              `
                <p>检测到接口错误${error}</p>
                <p>错误码<span style="color:red"> 404 </span>：此类错误多为接口未注册（或未重启）或者请求路径（方法）与api路径（方法）不符--如果为自动化代码请检查是否存在空格</p>
                `,
              "接口报错",
              {
                dangerouslyUseHTMLString: true,
                distinguishCancelAndClose: true,
                confirmButtonText: "我知道了",
                cancelButtonText: "取消",
              }
            ).then(() => {
              router.push({ name: "login", replace: true });
            });
            break;
        }

        return {
          error: true,
          response: error.response,
        };
      }
    );
  }
  get<T = any>(
    url: string,
    data?: T,
    options?: Partial<ApiRequestConfig>
  ): Promise<ApiResult<T>> {
    return this.service.get(url, { params: data, ...options });
  }
  put<T = any, D = any>(
    url: string,
    data?: D,
    options?: Partial<ApiRequestConfig>
  ): Promise<ApiResult<T>> {
    return this.service.put(url, data, options);
  }
  post<T = any, D = any>(
    url: string,
    data?: D,
    options?: Partial<ApiRequestConfig>
  ): Promise<ApiResult<T>> {
    return this.service.post(url, data, options);
  }
  delete<T = any>(
    url: string,
    options?: Partial<ApiRequestConfig>
  ): Promise<ApiResult<T>> {
    return this.service.delete(url, options);
  }
}

export default new Service();
```

###

## MOCK

用于模拟后端的组件

### 安装

```bash
pnpm i mockjs vite-plugin-mock --save-dev
```

### vite.config.js

```bash
  plugins: [
    ...
    viteMockServe({
      // supportTs:false,
      // logger:false,
      mockPath: './src/mock/apis', //解析路径
      enable: true,
      watchFiles: true
    })
  ],
```

### 编写一个 mock 接口

`/src/mock/user.ts`

```ts
import type { MockMethod } from "vite-plugin-mock";

export default [
  {
    url: "/api/login", // 注意，这里只能是string格式
    method: "post",
    response: () => {
      return {
        code: 0,
        data: {
          token: "safdasdfas",
        },
      };
    },
  },
] as MockMethod[]; // 这里其实就是定义数据格式的，不了解的同学可以参考typescript的官方文档
```

## scss

scss 是 css 的一种预处理语言

### 安装

```bash
pnpm i sass sass-loader --save-dev
```

### vite.config.js

```js
  css: {
    // css预处理器
    preprocessorOptions: {
      scss: {
        // 引入 mixin.scss 这样就可以在全局中使用 mixin.scss中预定义的变量了
        // 给导入的路径最后加上 ;
        //additionalData: '@import "@/assets/main.scss";' //如果在main.ts中引入了，这里无需再次引用
      }
    }
  },
```

### 使用

页面标签中加`lang="scss"`即可，如果没安装`sass`加该标签会语法报错

```vue
<style lang="scss" scoped>
...
</style>
```
