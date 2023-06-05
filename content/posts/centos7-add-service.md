---
title: "CentOS 7设置开机启动服务，添加自定义系统服务"
date: 2023-03-10T23:38:32+08:00
keywords: ["centos"]
description: ""
categories: ["技术应用"]
tags: ["centos"]
thumbnail: ""
banner: ""
---

## 建立服务文件
```bash
vim /usr/lib/systemd/system/my.service 
```
## 编写my.service
```bash
[Unit]
Description=My Service
After=network.target

[Service]
Type=forking
User=root
Group=root
WorkingDirectory=/usr/local/bin
ExecStart=/usr/local/bin/my
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```
```bash
详情如下（正式配置文件中不要带注释，否则容易出错）
```
```bash
[Unit] #服务的说明
Description=My Service #服务的描述
After=network.target remote-fs.target nss-lookup.target  #表示服务类别

[Service]  #服务运行参数的设置
Type=forking #表示是后台运行的形式
User=root  #该命令作用的用户
Group=root #该命令作用的用户组
ExecStart=/usr/local/bin/my  #运行的命令
ExecStop=  #停止的命令
ExecReload #为重启命令
PrivateTmp=True #表示给服务分配独立的临时空间

Restart=always: #只要不是通过systemctl stop来停止服务，任何情况下都必须要重启服务，默认值为no
RestartSec=5: #重启间隔，比如某次异常后，等待5(s)再进行启动，默认值0.1(s)
StartLimitInterval: 10 #无限次重启，默认是10秒内如果重启超过5次则不再重启，设置为0表示不限次数重启
注意：启动、重启、停止命令全部要求使用绝对路径

[Install]
WantedBy=multi-user.target
```
## 执行systemctl daemon-reload 命令使修改生效
```bash
systemctl daemon-reload
```
## 操作
```bash
# 开机启动
systemctl enable my.service
# 不自动启动
systemctl disable my.service
# 启动服务
systemctl start my.service
# 重启
systemctl restart my.service
# 检查状态 
systemctl status my.service
systemctl is-active my.service（仅显示是否Active)
# 显示所有启动的服务
systemctl list-units --type=service
```