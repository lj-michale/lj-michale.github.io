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
- 痛点1：在发布、弹性等高峰期，集群控制面服务时断时续，甚至完全不可用 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
面对大流量请求，如果控制面没有自动弹性扩容能力，会无法对负载自适应、导致控制面服务不可用。<br>
例如：客户端存在高频度持续 LIST 集群中的大量资源，集群 apiserver/etcd 无法自动弹性就可能联动出现OOM。<br>
ACK Pro托管版K8s可以对控制面组件根据负载压力做HPA和VPA，可以有效解决该痛点。
</p>

- 痛点2：集群节点批量NotReady导致雪崩，严重影响业务！ <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
部分节点出现NotReady，节点上Pod被驱逐调度到健康节点，健康节点由于压力过大也变为NotReady，加剧产生了更多NotReady的节点，业务持续重启。<br>
ACK提供了托管节点池功能，可以对出现NotReady的异常节点治愈，重新拉会 Ready 状态，可以有效解决该痛点。
</p>

- 痛点3：业务高峰期需快速弹性，节点上拉取Pod镜像耗时长达分钟级，影响业务 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
节点上kubelet并发拉取镜像遇到网络带宽限制，需要镜像加速功能支持。<br>
ACR提供了基于DADI(Data Accelerator for Disaggregated Infrastructure)的按需镜像加载和P2P镜像加速的功能，可以加速镜像拉取，可以有效解决该痛点。
</p>

- 痛点4：Master节点/组件运维复杂度高，包含资源配置、参数调优、升级管理等 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
需要大量的线上场景分析和优化、故障处理、规模压测等，来分析、整理并落地最佳实践和配置。<br>
ACK Pro托管版K8s在全网的规模体量上万集群，具有自动弹性和生命周期管理的运维管理架构，有丰富的优化、应急处理等经验，持续将最佳实践和参数优化对托管组件升级。
</p>

### K8s稳定性体现
- 服务可用性 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>

- 处理时延 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>

- 请求QPS与流量吞吐 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>

- 资源水位 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>


## 高可用集群建设
### Kubernetes集群架构
![img](/images/posts/kubernetes/微信截图_20240412111533.png)<br>

### 高可用架构设计
#### 单Master集群

#### 多master节点集群
- [分类]()
```.text
1. 应用负载均衡集群
2. 存储高可用集群 
```

##### 应用负载均衡集群
- [集群架构]()
![img](/images/posts/kubernetes/微信截图_20240412153801.png)<br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
kubernetes多master集群是指使用多个master节点来提高集群的可用性和容错性的方案。
master节点是负责控制和管理集群中的资源和服务的节点，它运行着以下组件：
1.kube-apiserver：提供了HTTP REST接口的关键服务进程，是集群中所有资源的增、删、改、查等操作的唯一入口，也是集群控制的入口进程。
2.kube-scheduler：负责资源调度（Pod调度）的进程，相当于公交公司的“调度室”。
3.kube-controller-manager：集群中所有资源对象的自动化控制中心，可以将其理解为资源对象的“大总管”。

实现kubernetes master集群有多种方式，根据不同的需求和场景，可以选择合适的方式来搭建和运维master集群。
一般来说，根据实现方式，负载均衡集群可以分为以下几种方案：
1.硬件负载均衡：硬件负载均衡是使用专门的硬件设备来实现负载均衡的方案，如 F5、Cisco 等。硬件负载均衡的优点是性能高、稳定性强，缺点是成本高、扩展性差。
2.软件负载均衡：软件负载均衡是使用普通的服务器和软件来实现负载均衡的方案，如 Nginx、HAProxy 等。软件负载均衡的优点是成本低、扩展性好，缺点是性能低、稳定性差。
3.混合负载均衡：混合负载均衡是结合硬件和软件来实现负载均衡的方案，如使用硬件设备作为全局入口，使用软件作为局部分发。混合负载均衡的优点是兼顾了性能和成本，缺点是复杂度高、维护难。
</p>


##### 











## 参考资料
- [使用kubeadm创建一个高可用etcd集群](https://v1-25.docs.kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)
- [lvs+keepalived部署kubernetes(k8s)高可用集群](https://www.cnblogs.com/liuqingliang/p/12987270.html)
- [Kubernetes核心架构与高可用集群详解](https://zhuanlan.zhihu.com/p/444114515)














