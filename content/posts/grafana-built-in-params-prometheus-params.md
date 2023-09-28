---
title: "Grafana内置参数，以及查询Prometheus参数"
date: 2023-09-26T19:11:19+08:00
keywords: ["Grafana","Prometheus"]
description: ""
categories: [""]
tags: [""]
thumbnail: ""
banner: ""
---

## Grafana内置参数
### $__dashboard
当前dashboard的名称

### $__from $__to
时间范围的毫秒值
可自定义格式，比如:{$__from: date :YYYY-MM-DD HH:mm:ss} {$__from: date :seconds}

### $__interval
查询的时间间隔，包含单位，比如：30s，2m

### $__interval_ms
查询的时间间隔，毫秒值

### $__range
查询的时间区间大小，包含单位，比如：2d

### $__range_s $__range_ms
查询的时间区间大小，分别是秒数和毫秒数

### $__timeFilter
返回当前选择的时间范围表达式，比如：time > now() -7d，常用于数据库作为datasource的时候。

## Prometheua查询变量
|Name	|Description|
|:-|:-|
|label_names()	|返回标签名称列表。|
|label_values(label)	|返回每个指标中标签的标签值列表。|
|label_values(metric{condiation}, label)	|返回指定度量中标签的标签值列表。也可以设置label为metric限定范围，且grafana中设置多个参数时支持联动。|
|metrics(metric)	|返回与指定度量正则表达式匹配的度量列表。|
|query_result(query)	|返回查询的Prometheus查询结果列表。|