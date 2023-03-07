---
title: "k8s之service.yaml 详细解析"
date: 2023-03-07T23:56:48+08:00
keywords: ["k8s","云原生"]
description: ""
categories: ["技术文档"]
tags: ["k8s","云原生"]
thumbnail: ""
banner: ""
---

```yaml
apiVersion: v1  #service API版本， service.apiVersion  
kind: Service #类型为service  
metadata: #定义service元数据，service.metadata  
  name: sgame-node-s10000  # 定义Service名称
  namespace: jszx  #定义命名空间
  labels: #自定义标签，service.metadata.labels
    app: sgame-node-s10000  #定义service标签的内容
  annotations:   # 备注
    creator: admin # 创建人
spec:  #定义service的详细信息，service.spec  
  ports:
    - name: http-10001    # 定义端口名称
      protocol: TCP       # 定义协议
      port: 10001         # service端口
      targetPort: 10001   # 目标pod端口
      nodePort: 31000     # 对外暴露的端口
    - name: http-3600
      protocol: TCP
      port: 3600
      targetPort: 3600
      nodePort: 32000
  selector:  #service的标签选择器，定义要访问的目标pod
    app: sgame-s10000  #将流量路到选择的pod上，须等于Deployment.spec.selector.matchLabels
    version: v1 # 同上两者条件满足时生效
  type: NodePort  #service的类型，定义服务的访问方式，默认为ClusterIP， service.spec.type
  sessionAffinity: None
  externalTrafficPolicy: Cluster
```