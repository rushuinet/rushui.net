---
title: "Grafana查询Prometheus参数"
date: 2023-03-20T23:17:25+08:00
keywords: ["Grafana","Prometheus"]
description: ""
categories: ["技术文档"]
tags: ["Grafana","Prometheus"]
thumbnail: ""
banner: ""
---

label_names() 返回标签名称列表

label_values(label) 返回Promthues所有监控指标中，标签名为label的所有可选值

label_values(metric{condiation}, label) 返回Promthues所有监控指标metric中，标签名为label的所有可选值， 也可以设置label为metric限定范围，且grafana中设置多个参数时支持联动。

metrics(metric) 返回所有指标名称满足metric定义正则表达式的指标名称

query_result(query) 返回prometheus查询语句的查询结果