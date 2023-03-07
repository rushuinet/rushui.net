---
title: "k8s的Deployment.yaml配置文件字段详解"
date: 2023-03-07T23:55:12+08:00
keywords: ["k8s","云原生"]
description: ""
categories: ["技术文档"]
tags: ["k8s","云原生"]
thumbnail: ""
banner: ""
---
```yaml
apiVersion: extensions/v1beta1  # 指定api版本，此值必须在kubectl api-versions中  
kind: Deployment  # 指定创建资源的角色/类型   
metadata:  # 资源的元数据/属性 
  name: demo  # 资源的名字，在同一个namespace中必须唯一
  namespace: default # 部署在哪个namespace中
  labels:  # 设定资源的标签
    app: nginx
    version: v1
spec: # 资源规范字段
  replicas: 1 # 声明副本数目
  revisionHistoryLimit: 3 # 保留历史版本
  selector: # 选择器
    matchLabels: # 匹配标签
      app: nginx
      version: v1
  minReadySeconds: 30 #定义新建的 Pod 经过多少秒后才被视为可用
  terminationGracePeriodSeconds: 30 #30秒内 (默认 30s) 还未完全停止，就发送 SIGKILL 信号强制杀死进程。
  progressDeadlineSeconds: 600 #升级过程中的最大时间(如果升级过程被暂停了，该时间也会同步暂停，时间不会一直增长)
  strategy: # 策略
    rollingUpdate: # 滚动更新
      maxSurge: 30% # 最大额外可以存在的副本数，可以为百分比，也可以为整数
      maxUnavailable: 30% # 示在更新过程中能够进入不可用状态的 Pod 的最大值，可以为百分比，也可以为整数
    type: RollingUpdate # 滚动更新策略
  template: # 模版
    metadata: # 资源的元数据/属性 
      annotations: # 自定义注解列表
        sidecar.istio.io/inject: "false" # 自定义注解名字
      labels: # 设定资源的标签
        app: nginx
        version: v1
    spec: # 资源规范字段
      containers:
      - name: nginx# 容器的名字   
        image: nginx:1.17.0 # 容器使用的镜像地址   
        imagePullPolicy: IfNotPresent # 每次Pod启动拉取镜像策略，三个选择 Always、Never、IfNotPresent
                                      # Always，每次都检查；
                                      # Never，每次都不检查（不管本地是否有）；
                                      # IfNotPresent，如果本地有就不检查，如果没有就拉取（手动测试时，已经打好镜像存在docker容器中时，
                                      #    使用存在不检查级别， 默认为每次都检查，然后会进行拉取新镜像，因镜像仓库不存在，导致部署失败）
        volumeMounts:		#文件挂载目录，容器内配置
        - mountPath: /data/		#容器内要挂载的目录
          name: share	    #定义的名字，需要与下面vloume对应
        resources: # 资源管理
          limits: # 最大使用
            cpu: 300m # CPU，1核心 = 1000m
            memory: 500Mi # 内存，1G = 1000Mi
          requests:  # 容器运行时，最低资源需求，也就是说最少需要多少资源容器才能正常运行
            cpu: 100m
            memory: 100Mi
        livenessProbe: # pod 内部健康检查的设置
          httpGet: # 通过httpget检查健康，返回200-399之间，则认为容器正常
            path: /healthCheck # URI地址
            port: 8080 # 端口
            scheme: HTTP # 协议
            # host: 127.0.0.1 # 主机地址
          initialDelaySeconds: 30 # 表明第一次检测在容器启动后多长时间后开始
          timeoutSeconds: 5 # 检测的超时时间
          periodSeconds: 30 # 检查间隔时间
          successThreshold: 1 # 成功门槛
          failureThreshold: 5 # 失败门槛，连接失败5次，pod杀掉，重启一个新的pod
        readinessProbe: # Pod 准备服务健康检查设置
          httpGet:
            path: /healthCheck
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 30
          timeoutSeconds: 5
          periodSeconds: 10
          successThreshold: 1
          failureThreshold: 5
      	#也可以用这种方法   
      	#exec: 执行命令的方法进行监测，如果其退出码不为0，则认为容器正常   
      	#  command:   
      	#    - cat   
      	#    - /tmp/health   
      	#也可以用这种方法   
      	#tcpSocket: # 通过tcpSocket检查健康  
      	#  port: number 
        ports:
          - name: http # 名称
            containerPort: 8080 # 容器开发对外的端口 
            protocol: TCP # 协议
      imagePullSecrets: # 镜像仓库拉取密钥
        - name: harbor-certification
      volumes:		#挂载目录在本机的路径
      - name: share	#对应上面的名字
        hostPath:
          path: /data	#挂载本机的路径
      affinity: # 亲和性调试
        nodeAffinity: # 节点亲和力
          requiredDuringSchedulingIgnoredDuringExecution: # pod 必须部署到满足条件的节点上
            nodeSelectorTerms: # 节点满足任何一个条件就可以
            - matchExpressions: # 有多个选项，则只有同时满足这些逻辑选项的节点才能运行 pod
              - key: beta.kubernetes.io/arch
                operator: In
                values:
                - amd64

```