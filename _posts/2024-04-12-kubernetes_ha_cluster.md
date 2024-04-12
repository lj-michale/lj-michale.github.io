---
layout: post
title:  Kubernetes | 基于Keepalived和HAproxy构建高可用Kubernetes集群
categories: [Kubernetes]
description: 基于Keepalived和HAproxy构建高可用Kubernetes集群
keywords: Kubernetes
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 前言 <br>
高可用Kubernetes集群通常使用多个节点运行控制平面组件，并且这些组件通常会有备份或者多个副本以防止单点故障。节点层面的工作负载则会被调度到多个健康节点上。

## 背景需求
### K8s常见的稳定性痛点
- [痛点1：在发布、弹性等高峰期，集群控制面服务时断时续，甚至完全不可用]()
```.text

```

- [痛点2：集群节点批量NotReady导致雪崩，严重影响业务！]()
```.text

```

- [痛点3：业务高峰期需快速弹性，节点上拉取Pod镜像耗时长达分钟级，影响业务]()
```.text

```

- [痛点4：Master节点/组件运维复杂度高，包含资源配置、参数调优、升级管理等]()
```.text

```




## 参考资料
- [使用kubeadm创建一个高可用etcd集群](https://v1-25.docs.kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)
- [lvs+keepalived部署kubernetes(k8s)高可用集群](https://www.cnblogs.com/liuqingliang/p/12987270.html)
- [Kubernetes核心架构与高可用集群详解](https://zhuanlan.zhihu.com/p/444114515)














