---
layout: post
title:  Platform | 基于开源构建的一站式数据算法服务平台(部署)
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
基于开源构建的一站式数据算法服务平台部署


## 背景需求
```.text
zVl66TL7pz106ub9M8zceWOfT6Rtv7FCfTYlGYNMGig=
```

## 准备工作
### Kubernetes(k8s)多主的高可用集群部署
#### 集群架构
![img](/images/posts/k8s/微信图片_20240406124928.png)<br>
#### 服务器
```.text
系统：CentOS8,内核版本：
```
- [服务器规划]()

|  服务器名称  | IP | 组件 | 角色 | 备注  |
|:-------:|:--:|:--:|:--:|:---:|
| master1 |    |  kubectl、kubeadm、kubelet、keepalived、haproxy  |    |     |
| master2 |    |   kubectl、kubeadm、kubelet、keepalived、haproxy |    |     |
| master3 |    |    |    |     |
| slave1  |    |  kubectl、kubeadm、kubelet  |    |     |
| slave2  |    |    |    |     |
| slave3  |    |    |    |     |
| slave4  |    |    |    |     |
| slave5  |    |    |    |     |
| slave6  |    |    |    |     |
| slave7  |    |    |    |     |
| slave8  |    |    |    |     |
| slave9  |    |    |    |     |

#### 组件及其版本

|     组件     |                                       类别                                        |   版本    | 下载地址 | 备注  |
|:----------:|:-------------------------------------------------------------------------------:|:-------:|:----:|:---:|
| Kubernetes |                  Kubernetes 也称为 K8s，是用于自动部署、扩缩和管理容器化应用程序的开源系统                   |  v1.26  |      |     |
| KubeSphere | KubeSphere 是在 Kubernetes 之上构建的以应用为中心的多租户容器平台,提供全栈的 IT 自动化运维的能力,简化企业的 DevOps 工作流 | v3.4.0  |      |     |
|   Docker   |                                      容器引擎                                       | 20.10.7 |      |     |
| HAProxyHAProxy |             keepalived是集群管理中保证集群高可用的一个服务软件，其功能类似于heartbeat，用来防止单点故障             |         |      |     |
|  HAProxy   | 开源的、高性能的、基于TCP(第四层)和HTTP(第七层)应用的负载均衡软件,借助HAProxy可以快速、可靠地提供基于TCP和HTTP应用的负载均衡解决方案 |  |      |     |

#### 环境准备
- [关闭防火墙]()
- [关闭SELinux]()
- [关闭Swap分区]()
- [修改主机名称解析]()
- [设置主机名称]()
- [转发IPv4并让iptables看到桥接流量]()
- [升级操作系统内核]()
  - [导入elrepo gpg key]()
  - [安装elrepo YUM源仓库]()
  - [安装kernel-lt版本]()
  - [设置grub2默认引导为0]()
  - [重新生成grub2引导文件]()

#### Docker部署
- [配置Docker镜像加速器]()
  ```.text

  ```

#### Containerd部署




#### KubeSphere部署




### K8S集成Zadig实现CI/CD DevOps
#### 组件及其版本

|   组件   |                     类别                      |   版本   | 下载地址 | 备注  |
|:------:|:-------------------------------------------:|:------:|:----:|:---:|
| Zadig  |   云原生 DevOps 平台,覆盖从需求到开发、测试、运维的一体化工程技术底座    | v2.2.0 |      |     |
| Harbor |       |  |      |     |
| GitLab |       |  |      |     |
| GitHub |       |  |      |     |
| Maven  |       |        |      |     |
|  Node  |       |        |      |     |
|  Java  |       |        |      |     |
| Scala  |       |        |      |     |
| Python |       |        |      |     |
|  pnpm  |       |        |      |     |
|  nvm   |       |        |      |     |
|  pip   |       |        |      |     |













## Q&A
- [Q: 使用yum命令时报错：CentOS Linux 8 - AppStream 错误：为仓库 ‘appstream’ 下载元数据失败 : Cannot prepare internal mirrorlist: No URLs in mirrorlist]()<br>
  ![img](/images/posts/k8s/微信图片_20240404105041.png)<br>

- [Q: K8S集群为何要使用负载均衡器VIP(HAProxy、HAProxy)]()<br>
  ![img](/images/posts/k8s/微信截图_20240405155227.png)<br>




## 名称解释

|      名称       |                                         解释说明                                          |  备注   | 
|:-------------:|:-------------------------------------------------------------------------------------:|:-----:|
|    kubectl    |                                       xxxcbxbxa                                       |  |
|  keepalived   |                                                                                       |       | 
|    haproxy    |                                                                                       |       | 
|      pod      |           pod是k8s的最小工作单元。每个pod包含一个或者多个容器。pod中的容器会作为一个整体被master调度到一个node上运行            |       | 
|  controller   |  k8s通常不会直接创建pod,而是通过controller来管理pod的。controller中定义了pod的部署特性，比如有几个副本，在什么样的node上运行等。   |       | 
|    slave1     |                                                                                       |       | 
|    slave1     |                                                                                       |       | 
|    slave1     |                                                                                       |       | 
|    slave1     |                                                                                       |       | 
|    slave1     |                                                                                       |       | 
|    slave1     |                                                                                       |       | 
|    slave1     |                                                                                       |       | 











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
- [K8S高可用集群搭建之负载均衡器VIP（HAProxy、keepalived）](https://www.bilibili.com/video/BV13c411p7fu/?spm_id_from=333.337.search-card.all.click&vd_source=b1ac49461ca56388666e71fc70402332)
- [阿里云官方镜像加速](https://help.aliyun.com/zh/acr/user-guide/accelerate-the-pulls-of-docker-official-images)
- [docker安装与配置docker镜像加速器](https://blog.csdn.net/wish_you_luck/article/details/130598016)
- [离线安装K8S集群1.25.3](https://www.bilibili.com/video/BV1fk4y1Y7AH/?p=153&spm_id_from=pageDriver&vd_source=b1ac49461ca56388666e71fc70402332)
- [使用kubeadm极速部署生产级kubernetes（k8s 1.29）集群](https://www.bilibili.com/video/BV1LC4y1g7wz?p=3&vd_source=b1ac49461ca56388666e71fc70402332)
- [基于kubeadm部署k8s 1.27版本高可用集群](https://www.bilibili.com/video/BV1QV4y1r7NR/?spm_id_from=333.337.search-card.all.click&vd_source=b1ac49461ca56388666e71fc70402332)
- [containerd简介](https://www.cnblogs.com/liwenchao1995/p/16896150.html)
- [使用Keepalived和HAproxy创建高可用Kubernetes集群](https://www.kubesphere.io/zh/docs/v3.3/installing-on-linux/high-availability-configurations/set-up-ha-cluster-using-keepalived-haproxy/)
- [HAProxy的配置与搭建](https://blog.csdn.net/ZZZ_CCC01/article/details/132404465)
- [HAproxy配置详解](https://cloud.tencent.com/developer/article/2034982)
- [使用kubeadm方式搭建多master高可用K8s集群](https://blog.csdn.net/wuhuayangs/article/details/125995743)
- [k8s-kubernetes--高可用集群的搭建](https://blog.csdn.net/Gong_yz/article/details/129658373)
- [探索Kubernetes的高可用性：单master集群和多master节点集群方案](https://blog.csdn.net/citywu123/article/details/132740625?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-132740625-blog-129901518.235%5Ev43%5Epc_blog_bottom_relevance_base4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-132740625-blog-129901518.235%5Ev43%5Epc_blog_bottom_relevance_base4&utm_relevant_index=2)
- [k8s pod常用运维命令](https://blog.csdn.net/nanhai_happy/article/details/134433603)
- [Zadig面向开发者的云原生DevOps平台](https://www.bilibili.com/video/BV19G411Q7zY/?spm_id_from=333.337.search-card.all.click&vd_source=b1ac49461ca56388666e71fc70402332)
- [k8s ingress基础](https://blog.csdn.net/weixin_40579389/article/details/134338494)
- [k8s-ingress-安装](https://blog.csdn.net/little_startoo/article/details/136005115)
- [云原生Java架构师的第一课K8s+Docker+KubeSphere+DevOps](https://www.bilibili.com/video/BV13Q4y1C7hS?p=70&vd_source=b1ac49461ca56388666e71fc70402332)
- [Linux软锁cpu,记一次服务器内核软死锁（soft lockup）解决方案](https://blog.csdn.net/weixin_30399511/article/details/116876978)


