---
title: "kube-state-metrics常见监控指标"
date: 2023-03-05T23:15:33+08:00
keywords: ["k8s","metrics","监控"]
description: ""
categories: ["技术文档"]
tags: ["metrics","k8s","监控告警"]
thumbnail: ""
banner: ""
---

## pod

```bash
kube_pod_info # 有关pod的信息。
kube_pod_start_time # pod的unix时间戳记中的开始时间。
kube_pod_completion_time #pod的unix时间戳记中的完成时间。
kube_pod_owner # 有关Pod所有者的信息。
kube_pod_labels # Kubernetes标签转换为Prometheus标签。
kube_pod_status_phase # Pod当前阶段。
kube_pod_status_ready # 描述容器是否准备好处理请求。
kube_pod_status_scheduled # 描述pod的调度过程的状态。
kube_pod_container_info # 有关容器中container的信息。
kube_pod_container_status_waiting # 描述容器当前是否处于等待状态。
kube_pod_container_status_waiting_reason # 描述容器当前处于等待状态的原因。
kube_pod_container_status_running # 描述容器当前是否处于运行状态。
kube_pod_container_status_terminated # 描述容器当前是否处于终止状态。
kube_pod_container_status_terminated_reason # 描述容器当前处于终止状态的原因。
kube_pod_container_status_last_terminated_reason # 描述容器处于终止状态的最后原因。
kube_pod_container_status_ready # Describes whether the containers readiness check succeeded.
kube_pod_container_status_restarts_total # 每个容器的容器重新启动次数。
kube_pod_container_resource_requests # 容器请求的请求资源数。
kube_pod_container_resource_limits # 容器请求的限制资源数量。
kube_pod_overhead 
kube_pod_created # Unix创建时间戳。
kube_pod_deletion_timestamp # Unix删除时间戳
kube_pod_restart_policy # 描述此pod使用的重新启动策略。
kube_pod_init_container_info # 有关Pod中init容器的信息。
kube_pod_init_container_status_waiting # ，描述初始化容器当前是否处于等待状态。
kube_pod_init_container_status_waiting_reason # Describes the reason the init container is currently in waiting state.
kube_pod_init_container_status_running # 描述初始化容器当前是否处于运行状态。
kube_pod_init_container_status_terminated # 描述初始化容器当前是否处于终止状态。
kube_pod_init_container_status_terminated_reason # 描述初始化容器当前处于终止状态的原因。
kube_pod_init_container_status_last_terminated_reason # 描述初始化容器处于终止状态的最后原因。
kube_pod_init_container_status_ready # 描述初始化容器准备情况检查是否成功。
kube_pod_init_container_status_restarts_total  #Counter类型，初始化容器的重新启动次数。    
kube_pod_init_container_resource_limits # 初始化容器请求的限制资源数。
kube_pod_spec_volumes_persistentvolumeclaims_info # 有关Pod中持久卷声明卷的信息。
kube_pod_spec_volumes_persistentvolumeclaims_readonly # 描述是否以只读方式安装了持久卷声明。
kube_pod_status_reason # pod状态原因
kube_pod_status_scheduled_time # Pod移至计划状态时的Unix时间戳
kube_pod_status_unschedulable # 描述pod的unschedulable状态。

```

## Deployment

```bash
kube_deployment_status_replicas  #deployment包含的副本个数
kube_deployment_status_replicas_available  #deployment的可用副本数
kube_deployment_status_replicas_unavailable  #deployment中不可用副本的数量
kube_deployment_status_replicas_updated  #deployment的更新副本数
kube_deployment_status_observed_generation  #deployment控制器观察到的生成
kube_deployment_status_condition  #部署的当前状态condition
kube_deployment_spec_replicas  #deployment所需的Pod数
kube_deployment_spec_paused  #deployment是否暂停，并且deployment控制器不会处理。
kube_deployment_spec_strategy_rollingupdate_max_surge  #滚动更新deployment期间的最大不可用副本数。
kube_deployment_metadata_generation  #代表期望状态的特定生成的序列号
kube_deployment_labels  #Kubernetes标签转换为Prometheus标签
kube_deployment_created  #Unix创建时间戳

```

## StatefulSet

```bash
kube_statefulset_status_replicas # StatefulSet的副本数。
kube_statefulset_status_replicas_current # StatefulSet的当前副本数。
kube_statefulset_status_replicas_ready #StatefulSet的就绪副本数。
kube_statefulset_status_replicas_updated #StatefulSet的更新副本数。
kube_statefulset_status_observed_generation #StatefulSet控制器观察到的生成。
kube_statefulset_replicas #StatefulSet所需的pod数。
kube_statefulset_metadata_generation #表示StatefulSet所需状态的特定生成的序列号。
kube_statefulset_created #Unix创建时间戳。
kube_statefulset_labels #Kubernetes标签转换为Prometheus标签。
kube_statefulset_status_current_revision #指示用于按顺序[0，currentReplicas）生成Pod的StatefulSet的版本。
kube_statefulset_status_update_revision #指示用于按顺序[replicas-updatedReplicas，replicas]生成Pod的StatefulSet的版本

```

## Endpoint

```bash
kube_endpoint_address_not_ready  #endpoint中not ready的addresses数
kube_endpoint_address_available  #endpoint中可用的addresses数
kube_endpoint_info  #有关endpoint的信息
kube_endpoint_labels  #Kubernetes标签转换为Prometheus标签
kube_endpoint_created  #Unix创建时间戳

```

## Node

```bash
kube_node_info # 有关群集节点的信息。
kube_node_labels # Kubernetes标签转换为Prometheus标签。
kube_node_role # 集群节点的角色。
kube_node_spec_unschedulable # 节点是否可以调度新的Pod。
kube_node_spec_taint # 群集节点的污点。
kube_node_status_capacity # 节点不同资源的容量。
kube_node_status_allocatable # 可用于调度的节点的不同资源的可分配资源。
kube_node_status_condition # 群集节点的状况。
kube_node_created # Unix创建时间戳。

```

## Service

```bash
kube_service_info # 有关service的信息。
kube_service_labels # Kubernetes标签转换为Prometheus标签。
kube_service_created # Unix创建时间戳。
kube_service_spec_type # Type about service.
kube_service_spec_external_ip # 服务外部IP。 每个IP一个组。
kube_service_status_load_balancer_ingress # 服务负载均衡器入口状态

```

## Secret

```bash
kube_secret_info # 有关secret的信息。
kube_secret_type # Type about secret.
kube_secret_labels # Kubernetes标签转换为Prometheus标签。
kube_secret_created # Unix创建时间戳。
kube_secret_metadata_resource_version # 代表secret特定版本的资源版本。

```

## DaemonSet

```bash
kube_daemonset_created  # Unix创建时间戳
kube_daemonset_status_current_number_scheduled  # 运行至少一个且应该运行的守护程序容器的节点数。
kube_daemonset_status_desired_number_scheduled  # 应该运行守护程序容器的节点数。
kube_daemonset_status_number_available  # 应该运行守护程序容器并具有一个或多个守护程序容器正在运行并且可用的节点数
kube_daemonset_status_number_misscheduled  # 运行守护程序容器但不应该运行的节点数。
kube_daemonset_status_number_ready  # 应该运行守护程序容器并已运行一个或多个守护程序容器并准备就绪的节点数。
kube_daemonset_status_number_unavailable  # 应该运行守护程序容器且没有任何守护程序容器正在运行并且可用的节点数
kube_daemonset_updated_number_scheduled  # 正在运行更新的守护程序pod的节点总数
kube_daemonset_metadata_generation  # 代表所需状态的特定生成的序列号。
kube_daemonset_labels  # Kubernetes标签转换为Prometheus标签。

```
