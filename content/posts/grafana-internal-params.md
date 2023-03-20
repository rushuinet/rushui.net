---
title: "Grafana内置参数"
date: 2023-03-20T23:18:08+08:00
keywords: ["Grafana"]
description: ""
categories: ["技术文档"]
tags: ["Grafana"]
thumbnail: ""
banner: ""
---

$__dashboard

当前dashboard的名称

__from__to

时间范围的毫秒值

可自定义格式，比如:{__from: date :YYYY-MM-DD HH:mm:ss} {__from: date :seconds}

$__interval

查询的时间间隔，包含单位，比如：30s，2m

$__interval_ms

查询的时间间隔，毫秒值

$__range

查询的时间区间大小，包含单位，比如：2d

__range_s__range_ms

查询的时间区间大小，分别是秒数和毫秒数

$__timeFilter

返回当前选择的时间范围表达式，比如：time > now() -7d，常用于数据库作为datasource的时候。