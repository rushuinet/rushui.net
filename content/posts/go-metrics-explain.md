---
title: "Go运行时metrics指标详解"
date: 2023-04-16T22:24:14+08:00
keywords: ["go", "metrics"]
description: "一个运行中的程序似乎很难具有“内省”能力，我们需要通过一个外部的观察者例如 dlv、gdb 来观察，或者是程序自身暴露的 metric。这就好像是要查看人体胃部的状况，需要借助外部工具胃镜，而内部的病变或者疼痛这样的指标也可以通过外部反应出来一样..."
categories: ["技术文档"]
tags: ["go", "metrics"]
thumbnail: ""
banner: ""
---

一个运行中的程序似乎很难具有“内省”能力，我们需要通过一个外部的观察者例如 dlv、gdb 来观察，或者是程序自身暴露的 metric。这就好像是要查看人体胃部的状况，需要借助外部工具胃镜，而内部的病变或者疼痛这样的指标也可以通过外部反应出来一样。这种 metric 暴露当然都是需要成本的。

对于 Go 程序来讲，大点的公司都有自己的监控平台，观测机器级别的(例如内存、CPU、网络、磁盘容量)、容器级别的（cgroup,cpu 抢占、cpu.throttled）、程序级别的(qps、请求延迟)等等。

虽然大部分指标都是某一时刻瞬时的指标，但是通过指定时间内拉取指标，观察指标的变化，我们就可以观察到程序在一段时间内运行状况。

在本文中笔者将介绍运行时暴露出来的一些指标，主要是内存方面的。其他 CPU 等指标可以通过 pprof 等其他手段获取到。这些指标对于观察程序的异常情况、并排查问题有重要意义。对一些指标的深刻理解需要对于 Go 运行时处理模型的理解，因此并不是一件简单的事情。本文希望提供对这些指标的准确表述，较少其他同学理解的成本。

要注意的是，我们可能有多种工具来观测相同的指标，例如 pprof、trace 都可以在不同程度表征程序内存的运行状态。不同的工具也有自己单独的指标，需要灵活的应用各种手段来达到自己想要的目的。

标准库获取运行时指标的函数有多个，Debug 库中的 ReadGCStats 方法可以返回运行时 GC 相关的关键指标，对于判断是否频繁发生 GC、以及 GC 完全不可用时长是否偏高有重要的意义。

```go
func ReadGCStats(stats *GCStats) {
	 ...
}
Debug库中的NumGoroutine获取程序的协程数量,GOMAXPROCS()获取逻辑处理器P。

func NumGoroutine() int {
	return int(gcount())
}

func GOMAXPROCS(n int) int[

}
```

runtime 标准库 ReadMemStats 函数也包含了运行时内存相关的指标，有一些指标比较难懂，涉及到对于 Go 内存模型的深刻理解。

```go
func ReadMemStats(m *MemStats) {
	...
}
```

当然了 runtime 还暴露了一些供 pprof 工具使用的 API，获取当前 Profile 样本文件，例如

```go
func MemProfile(p []MemProfileRecord, inuseZero bool) (n int, ok bool)
func MutexProfile(p []BlockProfileRecord) (n int, ok bool)
func ThreadCreateProfile(p []StackRecord) (n int, ok bool)
ReadMemStats 和 GCStats
```

在本文中， 笔者将对 ReadMemStats 得到的 MemStats 结构 和 ReadGCStats 中的 GCStats 结构中的各个指标进行解释。

```go
type MemStats struct {
		Alloc uint64
		TotalAlloc uint64
		Sys uint64
		...
}

type GCStats struct {
	LastGC         time.Time
	NumGC          int64
	PauseTotal     time.Duration
  ...
}
```

下面对各个指标进行深入解析：
Alloc
对象的分配字节数 = 已分配对象的字节数 - GC 已释放的对象的字节数

TotalAlloc
对象的分配数量 = 已分配对象的数量 - GC 已释放的对象的数量

Sys
运行时保留的虚拟内存，这些内存含义比较丰富，堆、栈和其他内部结构的内存

nlookup (已废弃！)

Mallocs
活着的对象数量（Mallocs - Frees），和 alloc 不同的是，包含了微小对象。

Frees
释放的对象数量

HeapAlloc
堆内存分配的对象的字节数，同 Alloc

HeapSys
向系统申请的虚拟内存大小

HeapIdle
向操作系统申请但是未分配或者回收了的堆内存 ， =HeapSys - HeapInuse

HeapInuse
被堆使用的内存，HeapInuse - HeapAlloc 表示这些内存还没有被分配对象。

HeapReleased
HeapIdle 中已经被释放给操作系统的内存。

HeapObjects
已分配的活着的堆对象 = Mallocs - Frees

StackInuse
协程栈本身占据的内存字节数

StackSys
= StackInuse 协程栈字节数 + 系统线程栈字节数

MSpanInuse
正在使用的的 span 的字节数

MSpanSys
操作系统分配的 span 的字节数，其包含了 MSpanInuse 和未被使用的 span

mcache_inuse
正在使用的的 mcache 的字节数

mcache_sys
操作系统分配的 mcache 的字节数，其包含了 mcache_inuse 和未被使用的 mcache

BuckHashSys
pprof 需要用到的 bucket 内存大小

GCSys
垃圾收集需要用到的元数据所占的内存大小。

OtherSys
运行时其他特殊对象所占的内存大小。

NextGC
下一次 GC 的目标内存大小

PauseTotalNs
程序累积的垃圾回收 stop-the-world 时间，即不可用时间。在 odin 为 ms

PauseNs
最近一次 stop-the-world 时间，即不可用时间

NumGC
程序开始后的 GC 次数，每一次 GC 加 1

NumForcedGC
用户强制触发的 GC 数量

GCCPUFraction
衡量 GC 花费的 CPU 时间
runtime/metrics
其实 Go 语言还提供了更通用的接口，完成 runtime.ReadMemStats 和 Debug.ReadGCStats 共同的功能，api 位于 runtime/metrics 中，生成当前时刻的 metric 样本。如下例中，简单打印出 metric Key-Value 对。

```go
package main

import (
	"fmt"
	"runtime/metrics"
)

func main() {
	// Get descriptions for all supported metrics.
	descs := metrics.All()

	// Create a sample for each metric.
	samples := make([]metrics.Sample, len(descs))
	for i := range samples {
		samples[i].Name = descs[i].Name
	}

	// Sample the metrics. Re-use the samples slice if you can!
	metrics.Read(samples)

	// Iterate over all results.
	for _, sample := range samples {
		// Pull out the name and value.
		name, value := sample.Name, sample.Value

		// Handle each sample.
		switch value.Kind() {
		case metrics.KindUint64:
			fmt.Printf("%s: %d\n", name, value.Uint64())
		case metrics.KindFloat64:
			fmt.Printf("%s: %f\n", name, value.Float64())
		case metrics.KindFloat64Histogram:
			// The histogram may be quite large, so let's just pull out
			// a crude estimate for the median for the sake of this example.
			fmt.Printf("%s: %f\n", name, medianBucket(value.Float64Histogram()))
		case metrics.KindBad:
			// This should never happen because all metrics are supported
			// by construction.
			panic("bug in runtime/metrics package!")
		default:
			// This may happen as new metrics get added.
			//
			// The safest thing to do here is to simply log it somewhere
			// as something to look into, but ignore it for now.
			// In the worst case, you might temporarily miss out on a new metric.
			fmt.Printf("%s: unexpected metric Kind: %v\n", name, value.Kind())
		}
	}
}

func medianBucket(h *metrics.Float64Histogram) float64 {
	total := uint64(0)
	for _, count := range h.Counts {
		total += count
	}
	thresh := total / 2
	total = 0
	for i, count := range h.Counts {
		total += count
		if total >= thresh {
			return h.Buckets[i]
		}
	}
	panic("should not happen")
}
```

这些 metric 具有与之前描述中相似的功能，可以对比着学习。

/gc/cycles/automatic:gc-cycles
运行时完成的 gc 次数

/gc/cycles/forced:gc-cycles
用户调用 force 完成的 gc 次数

/gc/cycles/total:gc-cycles
gc 总次数

/gc/heap/allocs-by-size:bytes
按照近似大小（Go 内存管理有各级 span 分别对应存储不同大小的对象）分配的堆大小

/gc/heap/allocs:bytes
堆内存分配的对象的字节数

/gc/heap/allocs:objects
堆内存分配的对象的个数

/gc/heap/frees-by-size:bytes
按照近似大小（Go 内存管理有各级 span 分别对应存储不同大小的对象）释放的内存大小

/gc/heap/frees:bytes
gc 释放的堆内存的累积总和

/gc/heap/frees:objects
gc 释放的堆内存的累积大小和

/gc/heap/goal:bytes
下一次 GC 内存目标

/gc/heap/objects:objects
占用堆内存的对象数，无论是活着的还是未清扫的

/gc/heap/tiny/allocs:objects
tiny 微小对象分配的个数

/gc/pauses:seconds
stw 时间

/memory/classes/heap/free:bytes
可以返回给操作系统的内存数量

/memory/classes/heap/objects:bytes
存活的对象或暂时未被 gc 标记的死对象大小

/memory/classes/heap/released:bytes
返回给操作系统的内存大小

/memory/classes/heap/stacks:bytes
当前协程栈占用的内存大小

/memory/classes/heap/unused:bytes
为堆对象保留但当前没有的内存

/memory/classes/metadata/mcache/free:bytes
为运行时 mcache 结构保留的内存，但未使用

/memory/classes/metadata/mcache/inuse:bytes
运行时 mcache 结构占用的内存，正在使用

/memory/classes/metadata/mspan/free:bytes
运行时 mspan 结构保留的内存，但未使用。

/memory/classes/metadata/mspan/inuse:bytes
运行时 mspan 结构占用的内存，目前正在使用

/memory/classes/metadata/other:bytes
为运行时保留或用于保存运行时元数据的内存

/memory/classes/os-stacks:bytes
底层操作系统分配的栈内存。

/memory/classes/other:bytes
调试运行时、finalizer 等分配的内存，用于特殊功能

/memory/classes/profiling/buckets:bytes
profiling 样本，栈扫描时使用的哈希表，占用的内存大小。

/memory/classes/total:bytes
Go 运行时映射到当前进程的所有内存。不包括通过 cgo 或 syscall 包里的代码映射的内存。

/sched/goroutines:goroutines
实时 goroutine 的计数。

/sched/latencies:seconds
协程在实际运行之前，在调度器中等待所花费的时间分布。
总结

本文对运行时暴露的指标进行了准确的表述，这些指标对于在一段时间周期内观察程序的运行状态，监控和排查问题有最重要意义。而要理解有些指标详细的含义，需要对于内存、虚拟内存、Go 内存模型的准确理解。
