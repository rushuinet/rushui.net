---
title: "Windows环境安装pnpm后无法在全局中使用"
date: 2024-07-27T17:38:13+08:00
keywords: ["pnpm"]
description: "pnpm : 无法加载文件 pnpm.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies"
categories: ["技术应用"]
tags: ["pnpm"]
thumbnail: ""
banner: ""
---
## 问题示例：
pnpm : 无法加载文件 E:\Program Files\nodejs\node_global\pnpm.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。 所在位置 行:1 字符: 1
pnpm
  + CategoryInfo          : SecurityError: (:) []，PSSecurityException
  + FullyQualifiedErrorId : UnauthorizedAccess

## 问题描述：
在pnpm的安装目录中可以正常使用pnpm，但是在其他目录中无法使用，环境变量中也已经正常配置。

错误原因：执行 pnpm 命令时出现了关于 PowerShell 执行策略的错误。

## 解决方法：
以管理员身份打开 PowerShell
 ```bash
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```
执行后即可在全局中使用pnpm。