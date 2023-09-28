---
title: "Prometheus sum 和 sum_over_time 的区别"
date: 2023-09-25T20:51:05+08:00
keywords: ["Prometheus","sum_over_time"]
description: ""
categories: ["技术应用"]
tags: ["Prometheus"]
thumbnail: ""
banner: ""
---

## sum
sum 是 Prometheus 内置的聚合操作，多个时序的数据会合并为单个时序的数据。不同时序的数据会相加在一起。sum 参数是瞬时向量（single instant vector）。

有如下两个时序数据：（间隔为秒）
```bash
request_count{a=1}  1,1,1,1,1
request_count{a=2}  1,1,1,1,1
```
执行 sum(request_count) 返回如下的时序：
```bash
{} 2,2,2,2,2
```
sum 也可以指定汇总的维度，如果执行 sum(request_count) by (a) 会返回如下的结果：
```bash
{a=1}  1,1,1,1,1
{a=2}  1,1,1,1,1
```
按照 a 的值相同的相加，所以上面还是两个时序。

## sum_over_time
sum_over_time 是单个时序中的一个区间内的度量值相加，sum_over_time 使用的是区间向量（range-vector） 。多个时序之间不会有合并的操作。

有如下两个时序数据：（间隔为秒）
```bash
request_count{a=1}  1,1,1,1,1
request_count{a=2}  1,1,1,1,1
```
执行 sum_over_time(request_count[5s]) 返回如下的时序：
```bash
request_count{a=1}  5
request_count{a=2}  5
```

## 相关文档
https://doc.cncf.vip/prometheus-handbook