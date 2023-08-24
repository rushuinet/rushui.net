---
title: "开发一个自己的前端CLI"
date: 2023-08-10T19:51:48+08:00
keywords: ["cli"]
description: "在前端开发中，拥有一套高效、简洁、适合自己的前端项目构建工具是非常有必要的。毕竟市面上不是所有的工具都能满足自己复杂工作的需要。此外也希望可以将此技术服务于其他的伙伴们。"
categories: ["技术应用"]
tags: ["cli"]
thumbnail: ""
banner: ""
---
## 前言
在前端开发中，拥有一套高效、简洁、适合自己的前端项目构建工具是非常有必要的。毕竟市面上不是所有的工具都能满足自己复杂工作的需要。此外也希望可以将此技术服务于其他的伙伴们。

## 搭建CLI准备
每一次成功，我们都是站在巨人的肩膀上。

首先简单介绍几个常用的依赖的包
- commander：命令行接口的完整解决方案，设置一些node命令，如help、usage、version、parse输入的参数。
- download-git-repo：git代码下载库，存到本地。
- chalk: node终端样式库，可以修改console的输出颜色。
- inquirer: 用户与命令行之间的交互问答工具。
- ora：终端旋转器，loading小圈圈。
- rimraf：删除文件。
## 初始化项目
创建项目文件夹，执行以下命令，创建项目package.json。
```bash
npm init
```
初始化结果
```json
{
  "name": "vadm",
  "version": "0.0.1",
  "description": "vue-admin cli tools",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "vue-admin",
    "vadm"
  ],
  "author": "rushuinet",
  "license": "ISC"
}
```
### 编辑项目
修改package.json文件，添加bin中的CLI将是我们执行的命令，和依赖库。
```json
{
    "name":"vadm",
    "version":"0.0.1",
    "description":"vue-admin cli tools",
    "main":"main.js",
    "bin":{
        "CLI":"./bin/cli"
    },
    "scripts":{
        "test":"echo \"Error: no test specified\" && exit 1"
    },
    "dependencies":{
        "inquirer":"^4.0.0",
        "ora":"^1.3.0",
        "rimraf":"^2.6.2",
        "chalk":"^2.3.0",
        "commander":"^2.11.0",
        "download-git-repo":"^1.0.1"
    },
    "keywords":[
        "cli"
    ],
    "author":"rushuinet",
    "license":"ISC"
}
```
新增`bin/vadm`目录文件，编写`bin/vadm`文件。
```js
#!/usr/bin/env node
//第一行其中#!/usr/bin/env node表示用node解析器执行本文件。

const program = require("commander");
const pkg = require("../package");
const chalk = require("chalk");
const download = require("download-git-repo");
const ora = require("ora");
const spinner = ora("Loading undead unicorns");

/**
 * version
 */
program.version(chalk.green(`${pkg.version}`));

/**
 * init 项目
 */
program
  .command("init <app-name>")
  .description(
    "generate a project from a remote template (legacy API, requires ./wk-init)"
  )
  .option("-c, --clone", "Use git clone when fetching remote template")
  .action((appName, opts, cmd) => {
    spinner.start("开始下载");
    download(
      "direct:https://github.com/vue-admin/vue-admin.git#main",
      appName,
      { clone: true },
      (err) => {
        if (err) {
          spinner.fail(chalk.green("下载失败 \n" + err));
          process.exit();
        }
        spinner.succeed(chalk.green(`下载成功`));
      }
    );
  });

program.parse(process.argv);
```
到此一个简单的cli就搭建完成。

## 本地调试
### 本地安装
```bash
npm install -g
OR
npm link // 添加软连接
```
### 调试
```bash
// 查看版本号
vadm -V
OR 
node bin/vadm -V

// 初始化项目
vadm init <app-name>
OR 
node bin/vadm init <app-name>
```
### 发布到npm
```bash
npm login 
npm publish
```
## 踩坑点
1.required ora和chalk错误问题: Error [ERR_REQUIRE_ESM]: require() of ES Module

高版本不支持require,目前改为以下版本解决：
```js
    "ora":"^5.1.0",
    "chalk":"^4.1.2",
```

2.Error: 'git clone' failed with status 128

解决：地址要保证使用的是https模式，地址加前缀，例如：
```bash
direct:https://github.com/vue-admin/vue-admin.git
```

3.Error: 'git checkout' failed with status 1

解决：保证你拥有master分支（不指定分支的情况），我之前的项目就只有一个分支叫main，不符合分支要求，所以会报错，但是他也会下载下来，只是提示不好看
但是如果指定分支的话不会报错，例如：
```bash
direct:https://github.com/vue-admin/vue-admin.git#main
```

## 项目地址
https://github.com/vue-admin/vadm