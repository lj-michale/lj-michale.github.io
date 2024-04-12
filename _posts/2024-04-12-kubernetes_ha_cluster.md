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
面对大流量请求，如果控制面没有自动弹性扩容能力，会无法对负载自适应、导致控制面服务不可用。
例如：客户端存在高频度持续 LIST 集群中的大量资源，集群 apiserver/etcd 无法自动弹性就可能联动出现OOM。
ACK Pro 托管版 K8s 可以对控制面组件根据负载压力做 HPA 和 VPA，可以有效解决该痛点。
```
- [痛点2：集群节点批量NotReady导致雪崩，严重影响业务！]()
```.text
部分节点出现NotReady，节点上Pod被驱逐调度到健康节点，健康节点由于压力过大也变为NotReady，加剧产生了更多NotReady的节点，业务持续重启。
ACK提供了托管节点池功能，可以对出现NotReady 的异常节点治愈，重新拉会 Ready 状态，可以有效解决该痛点。
```
- [痛点3：业务高峰期需快速弹性，节点上拉取Pod镜像耗时长达分钟级，影响业务]()
```.text
节点上 kubelet 并发拉取镜像遇到网络带宽限制，需要镜像加速功能支持。
ACR 提供了基于 DADI（Data Accelerator for Disaggregated Infrastructure）的按需镜像加载和 P2P 镜像加速的功能，可以加速镜像拉取，可以有效解决该痛点。
```
- [痛点4：Master节点/组件运维复杂度高，包含资源配置、参数调优、升级管理等]()
```.text
需要大量的线上场景分析和优化、故障处理、规模压测等，来分析、整理并落地最佳实践和配置。
ACK Pro 托管版 K8s 在全网的规模体量上万集群，具有自动弹性和生命周期管理的运维管理架构，有丰富的优化、应急处理等经验，持续将最佳实践和参数优化对托管组件升级。
```



















## 参考资料
- [使用kubeadm创建一个高可用etcd集群](https://v1-25.docs.kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)
- [lvs+keepalived部署kubernetes(k8s)高可用集群](https://www.cnblogs.com/liuqingliang/p/12987270.html)
- [Kubernetes核心架构与高可用集群详解](https://zhuanlan.zhihu.com/p/444114515)














