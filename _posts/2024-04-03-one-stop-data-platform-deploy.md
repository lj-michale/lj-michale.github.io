---
layout: post
title:  Platform | 基于开源构建的一站式数据与算法平台(部署)
categories: [data platform]
description: 基于开源构建的一站式数据与算法平台部署
keywords: platform
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 前言 <br>
基于开源构建的一站式数据与算法平台部署

## 背景需求
```.text

```

## 准备工作
### KubeSphere部署Kubernetes(k8s)多主的高可用集群
![img](/images/posts/k8s/微信截图_20240404095700.png)<br>
#### 服务器
```.text
CentOS8
```
#### 组件及其版本

|     组件     |                                       类别                                        |   版本    | 下载地址 | 备注  |
|:----------:|:-------------------------------------------------------------------------------:|:-------:|:----:|:---:|
| Kubernetes |                  Kubernetes 也称为 K8s，是用于自动部署、扩缩和管理容器化应用程序的开源系统                   |  v1.26  |      |     |
| KubeSphere | KubeSphere 是在 Kubernetes 之上构建的以应用为中心的多租户容器平台,提供全栈的 IT 自动化运维的能力,简化企业的 DevOps 工作流 | v3.4.0  |      |     |
|   Docker   |                                      容器引擎                                       | 20.10.7 |      |     |
| HAProxyHAProxy |             keepalived是集群管理中保证集群高可用的一个服务软件，其功能类似于heartbeat，用来防止单点故障             |         |      |     |
|  HAProxy   | 开源的、高性能的、基于TCP(第四层)和HTTP(第七层)应用的负载均衡软件,借助HAProxy可以快速、可靠地提供基于TCP和HTTP应用的负载均衡解决方案 |  |      |     |



### k8s集成Zadig实现CI/CD DevOps
#### 组件及其版本

|   组件   |                     类别                      |   版本    | 下载地址 | 备注  |
|:------:|:-------------------------------------------:|:-------:|:----:|:---:|
| Zadig  |   云原生 DevOps 平台,覆盖从需求到开发、测试、运维的一体化工程技术底座    |  v2.2.0  |      |     |
| Harbor |       |  v2.2.0  |      |     |
| GitLab |       |  v2.2.0  |      |     |
| GitHub |       |  v2.2.0  |      |     |




## Q&A
- [Q: 使用yum命令时报错：CentOS Linux 8 - AppStream 错误：为仓库 ‘appstream’ 下载元数据失败 : Cannot prepare internal mirrorlist: No URLs in mirrorlist]()<br>
  ![img](/images/posts/k8s/微信图片_20240404105041.png)<br>

- [Q: K8S集群为何要使用负载均衡器VIP(HAProxy、HAProxy)]()<br>
  ![img](/images/posts/k8s/微信截图_20240405155227.png)<br>




## 参考资料
- [Zadig为什么能用的爽](https://www.51cto.com/article/721329.html)
- [KubeSphere助力提升研发效能的应用实践分享](https://blog.csdn.net/zpf17671624050/article/details/130262829)
- [看了就会的Zadig入门教程](https://blog.csdn.net/a519781181/article/details/131198539)
- [zadig-2.1文档](https://www.bookstack.cn/read/zadig-2.1-zh/%e5%bf%ab%e9%80%9f%e5%85%a5%e9%97%a8.md)
- [Zadig官网](https://www.koderover.com/)
- [部署Kubernetes(k8s)多主的高可用集群](https://blog.csdn.net/m0_51510236/article/details/134142834)
- [kubesphere搭建k8s集群](https://blog.csdn.net/javajy/article/details/131389894)
- [kubesphere官网](https://kubesphere.io/)
- [如何使用 GitLab + Zadig 实现产品级持续交付](https://koderover.com/tutorials-detail/codelabs/GitLab/index.html?index=..%2F..index#0)
- [K8S部署Harbor（三部曲之三：使用）](https://blog.csdn.net/mo_sss/article/details/135961189)
- [K8S自动化部署Java项目的实现流程（Gitlab推送镜像到Harbor，K8S拉取Harbor中的镜像进行部署）](https://blog.csdn.net/mo_sss/article/details/137178236?spm=1001.2014.3001.5502)
- [zadig安装部署接入k8s集群、ldap、jenkins、gitlab、项目配置管理、yaml模板管理](https://blog.csdn.net/weixin_43606975/article/details/127442891)
- [K8S部署Harbor镜像仓库（含离线安装包harbor-offline-installer国内下载链接）](https://blog.csdn.net/mo_sss/article/details/135909921?spm=1001.2014.3001.5502)
- [K8S部署GitLab（详细完整版）](https://blog.csdn.net/mo_sss/article/details/135461021?spm=1001.2014.3001.5502)
- [K8S搭建（centos）完整版](https://blog.csdn.net/mo_sss/article/details/135930838?spm=1001.2014.3001.5502)


