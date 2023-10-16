---
title: "深入了解 K8s 扩展神器 client-go 的详细用法"
date: 2023-10-12T20:24:32+08:00
keywords: ["K8s", "client-go"]
description: "当使用 Kubernetes 进行应用程序的开发和部署时，client-go 是一个非常重要的工具。它是 Kubernetes 的官方客户端库，提供了与 Kubernetes ApiServer 进行通信的接口和实现。"
categories: ["技术应用"]
tags: ["K8s", "client-go"]
thumbnail: ""
banner: ""
---

## 1. 简介

当使用 Kubernetes 进行应用程序的开发和部署时，client-go 是一个非常重要的工具。它是 Kubernetes 的官方客户端库，提供了与 Kubernetes ApiServer 进行通信的接口和实现。

client-go 主要提供以下几个功能：

与 Kubernetes ApiServer 进行通信：client-go 提供了与 Kubernetes ApiServer 进行通信的接口和实现，包括基本的 http 请求和更深层次的封装。开发人员可以使用 client-go 创建、更新和删除 Kubernetes 中的资源。
访问 Kubernetes ApiServer 中的资源：client-go 提供了访问 Kubernetes ApiServer 中资源的方法，包括使用 ClientSet 进行基于对象的访问和使用 DynamicClient 进行基于无类型的访问。
处理 Kubernetes 资源的事件：client-go 提供了一种称为 Informer 的机制，它可以监听 Kubernetes ApiServer 中的资源变更事件。开发人员可以使用 Informer 实现资源的快速检索和本地缓存，从而减轻对 ApiServer 的访问压力。
发现 Kubernetes ApiServer 中的资源：client-go 还提供了 DiscoveryClient 接口，该接口可以用于在 Kubernetes ApiServer 中查找特定资源的详细信息。
总的来说，client-go 是 Kubernetes 开发人员不可或缺的工具之一。它提供了丰富的功能和灵活的接口，使开发人员能够更轻松地构建和管理 Kubernetes 应用程序。

## 2. Client

### 多种 client 介绍

RESTClient
最基础的客户端，其他的 ClientSet、DyanmicClient、DiscoveryClient 都是基于 RESTClient 实现的

ClientSet
对 k8s 内置资源的客户端集合。如获取 pod 资源对象的 client

clientset.CoreV1().Pods("")

DynamicClient
动态客户端，它可以对任意 k8s 资源进行 RESTful 操作，包括 CRD 资源。与 ClientSet 最大的不同是，ClientSet 仅能访问 k8s 自带的资源，不能直接访问 CRD 资源。

DiscoveryClient
发现客户端，主要用于发现 k8sApiServer 所支持的资源组，资源版本资源信息。

Clientset 和 dynamicClient 都是面向资源对象的（例如创建 deployment 实例、查看 pod 实例），而 DiscoveryClient 则不同，它聚焦的是资源

参考： https://www.cnblogs.com/bolingcavalry/p/15249712.html

### 加载kubeconfig及各客户端初始化的方法

```go
package config

import (
        "k8s.io/client-go/discovery"
        "k8s.io/client-go/dynamic"
        "k8s.io/client-go/kubernetes"
        "k8s.io/client-go/rest"
        "k8s.io/client-go/tools/clientcmd"
        "log"
)

const kubeConfigFilePath = "/Users/ShadowYD/.kube/config"

type K8sConfig struct {
}

func NewK8sConfig() *K8sConfig {
   return &K8sConfig{}
}
// 读取kubeconfig 配置文件
func (this *K8sConfig) K8sRestConfig() *rest.Config {
  config, err := clientcmd.BuildConfigFromFlags("", kubeConfigFilePath)
  if err != nil {
          log.Fatal(err)
  }
  return config
}
// 初始化 clientSet
func (this *K8sConfig) InitClient() *kubernetes.Clientset {
        c, err := kubernetes.NewForConfig(this.K8sRestConfig())

        if err != nil {
                log.Fatal(err)
        }

        return c
}

// 初始化 dynamicClient
func (this *K8sConfig) InitDynamicClient() dynamic.Interface {
        c, err := dynamic.NewForConfig(this.K8sRestConfig())

        if err != nil {
                log.Fatal(err)
        }

        return c
}

// 初始化 DiscoveryClient
func (this *K8sConfig) InitDiscoveryClient() *discovery.DiscoveryClient {
        return discovery.NewDiscoveryClient(this.InitClient().RESTClient())
}
```

### 示例

```go
package main

import (
    "context"
    "flag"
    "fmt"
    "path/filepath"
    "time"

    "k8s.io/apimachinery/pkg/api/errors"
    metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
    "k8s.io/client-go/kubernetes"
    "k8s.io/client-go/tools/clientcmd"
    "k8s.io/client-go/util/homedir"
)

func main() {
    var kubeconfig *string
    if home := homedir.HomeDir(); home != "" {
        kubeconfig = flag.String("kubeconfig", filepath.Join(home, ".kube", "config"), "(optional) absolute path to the kubeconfig file")
    } else {
        kubeconfig = flag.String("kubeconfig", "", "absolute path to the kubeconfig file")
    }
    flag.Parse()

    // 获取配置对象
    config, err := clientcmd.BuildConfigFromFlags("", *kubeconfig)
    if err != nil {
        panic(err.Error())
    }

    // 创建 clientset
    clientset, err := kubernetes.NewForConfig(config)
    if err != nil {
        panic(err.Error())
    }
    for {
        // 调用list方法时才会请求apiserver
        pods, err := clientset.CoreV1().Pods("").List(context.TODO(), metav1.ListOptions{})
        if err != nil {
            panic(err.Error())
        }
        fmt.Printf("There are %d pods in the cluster\n", len(pods.Items))

        // 获取pod信息
        namespace := "default"
        pod := "soa-test-service-5fd6fd67fd-7ktdg"
        _, err = clientset.CoreV1().Pods(namespace).Get(context.TODO(), pod, metav1.GetOptions{})
        if errors.IsNotFound(err) {
            fmt.Printf("Pod %s in namespace %s not found\n", pod, namespace)
        } else if statusError, isStatus := err.(*errors.StatusError); isStatus {
            fmt.Printf("Error getting pod %s in namespace %s: %v\n",
                pod, namespace, statusError.ErrStatus.Message)
        } else if err != nil {
            panic(err.Error())
        } else {
            fmt.Printf("Found pod %s in namespace %s\n", pod, namespace)
        }

        time.Sleep(10 * time.Second)
    }
}
```

## 3. Informer

关注点：indexer 、 informer 、事件注册机制等，下面这篇文章写的比较详细，这里不多做赘述。

参考：https://www.cnblogs.com/charlieroro/p/10330390.html
