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
Kubernetes的高可用主要指的是控制平面的高可用，简单说，就是有多套Master节点组件和Etcd组件，kube-apiserver使用负载平衡器暴露给工作节点，工作节点通过负载均衡连接到各Master。

## 背景需求
```.text

```






## 参考资料
- [使用kubeadm创建一个高可用etcd集群](https://v1-25.docs.kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)
- [lvs+keepalived部署kubernetes(k8s)高可用集群](https://www.cnblogs.com/liuqingliang/p/12987270.html)
- [Kubernetes核心架构与高可用集群详解](https://zhuanlan.zhihu.com/p/444114515)














