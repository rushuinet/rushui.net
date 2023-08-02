---
title: "Docker解决容器内访问宿主机'No route to host'的问题"
date: 2023-07-31T19:40:48+08:00
keywords: ["Docker"]
description: ""
categories: ["技术应用"]
tags: ["Docker"]
thumbnail: ""
banner: ""
---
## 问题
使用`docker`在虚拟机上搭建了`prometheus`服务器，并采集`etcd`、`jaeger`等服务的监控指标，有一天`prometheus`的targets中State显示异常，发现是不能访问宿主机IP导致。另外一个容器访问别外一个容器的数据库（也是通过主机IP）也不成功，应该是同一问题。
## 解决
主要原因在服务器屏蔽从docker内部的访问, 修改服务器的防火墙设置即可.

`ifconfig`查看docker0的IP，一般为`172.17.0.1`

在服务器内修改配置文件`vi /etc/firewalld/zones/public.xml`添加容器A的ip地址, 具体内容如下:
```bash
  <rule family="ipv4">
    <source address="172.17.0.0/16"/>
    <accept/>
  </rule>
```
也可用命令添加:
```bash
firewall-cmd --permanent --zone=public --add-rich-rule='rule family=ipv4 source address=172.17.0.0/16 accept'
```

如果以上操作问题还是不能解决，可尝试开启防火墙端口：
```bash
firewall-cmd --zone=public --add-port=3306/tcp --permanent  # mysql
firewall-cmd --zone=public --add-port=2379/tcp --permanent  # etcd
firewall-cmd --zone=public --add-port=14269/tcp --permanent # jaeger
firewall-cmd --reload
```
这些命令主要是修改`vi /etc/firewalld/zones/public.xml`文件，操作后的内容如下：
```xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="ssh"/>
  <service name="dhcpv6-client"/>
  <port protocol="tcp" port="20"/>
  <port protocol="tcp" port="21"/>
  <port protocol="tcp" port="22"/>
  <port protocol="tcp" port="80"/>
  <port protocol="tcp" port="443"/>
  <port protocol="tcp" port="3306"/>
  <port protocol="tcp" port="2379"/>
  <port protocol="tcp" port="14269"/>
  <rule family="ipv4">
    <source address="172.17.0.0/16"/>
    <accept/>
  </rule>
</zone>
```
如果还存在问题的话，重启docker
```bash
systemctl restart docker.service
```