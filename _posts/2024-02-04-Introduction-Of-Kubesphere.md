---
layout: post
title: Kubesphere | Kubesphere整理与总结
categories: [Kubesphere]
description: Kubesphere整理与总结
keywords: Kubesphere、Kubernetes
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## Kubesphere整理与总结

---------------
- [什么是KubeSphere]()
  ```.text
   KubeSphere 是在 Kubernetes 之上构建的面向云原生应用的分布式操作系统，完全开源，支持多云与多集群管理，提供全栈的 IT 自动化运维能力，简化企业的 DevOps 工作流。它的架构可以非常方便地使第三方应用与云原生生态组件进行即插即用 (plug-and-play) 的集成。
   KubeSphere 是在 Kubernetes 之上构建的以应用为中心的企业级分布式容器平台，提供简单易用的操作界面以及向导式操作方式，在降低用户使用容器调度平台学习成本的同时，
   极大减轻开发、测试、运维的日常工作的复杂度，旨在解决 Kubernetes 本身存在的存储、网络、安全和易用性等痛点。除此之外，平台已经整合并优化了多个适用于容器场景的功能模块，
   以完整的解决方案帮助企业轻松应对敏捷开发与自动化运维、DevOps、微服务治理、灰度发布、多租户管理、工作负载和集群管理、监控告警、日志查询与收集、服务与网络、应用商店、镜像构建与镜像仓库管理和存储管理等多种业务场景。
   后续版本还将提供和支持多集群管理、大数据、人工智能等更为复杂的业务场景。
  ```
  ```.text
   KubeSphere 从项目初始阶段就采用开源的方式来进行项目的良性发展，相关的项目源代码和文档都在 GitHub 可见。
   KubeSphere 支持部署和运行在包括公有云、私有云、VM、BM 和 Kubernetes 等任何基础设施之上，并且支持在线安装与离线安装，目前已在 阿里云、腾讯云、华为云、青云、AWS、Kubernetes 上进行过部署测试。功能架构
  ```

- [功能架构]()
  ```.text
   KubeSphere 2.1 提供了全栈化容器部署与管理平台，支持在生产环境安装与使用，它的核心功能可以概括在以下的功能架构图中
  ```
  ![img](/images/posts/kubesphere/微信截图_20240118144150.png)<br>
  ![img](/images/posts/kubesphere/20190910101703.png)<br>
  ![img](/images/posts/kubesphere/微信截图_20240118152152.png)<br>

- [平台功能]()
  ```.text
   KubeSphere 作为开源的企业级全栈化容器平台，为用户提供了一个具备极致体验的 Web 控制台，让您能够像使用任何其他互联网产品一样，快速上手各项功能与服务。
   KubeSphere 目前提供了工作负载管理、微服务治理、DevOps 工程、Source to Image/Binary to Image、多租户管理、多维度监控、日志查询与收集、告警通知、服务与网络、应用管理、基础设施管理、镜像管理、应用配置密钥管理等功能模块，
   开发了适用于物理机部署 Kubernetes 的 负载均衡器插件 Porter，并支持对接多种开源的存储与网络方案，支持高性能的商业存储与网络服务。
  ```
  ![img](/images/posts/kubesphere/微信截图_20240118144334.png)
    - [Kubernetes资源管理]()
      ```.text
       对底层 Kubernetes 中的多种类型的资源提供可视化的展示与监控数据，以向导式 UI 实现工作负载管理、镜像管理、服务与应用路由管理 (服务发现)、密钥配置管理等，并提供弹性伸缩 (HPA) 和容器健康检查支持，支持数万规模的容器资源调度，保证业务在高峰并发情况下的高可用性。
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118144451.png)
    - [微服务治理]()
      ```.text
       1.灵活的微服务框架：基于 Istio 微服务框架提供可视化的微服务治理功能，将 Kubernetes 的服务进行更细粒度的拆分
       2.完善的治理功能：支持熔断、灰度发布、流量管控、限流、链路追踪、智能路由等完善的微服务治理功能，同时，支持代码无侵入的微服务治理
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118144601.png)
    - [多租户管理]()
      ```.text
       1.多租户：提供基于角色的细粒度多租户统一认证与三层级权限管理
       2.统一认证：支持与企业基于 LDAP / AD 协议的集中认证系统对接，支持单点登录 (SSO)，以实现租户身份的统一认证
       3.权限管理：权限等级由高至低分为集群、企业空间与项目三个管理层级，保障多层级不同角色间资源共享且互相隔离，充分保障资源安全性
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118144655.png)
      ```.text
       目前，平台的资源一共有三个层级，包括 集群 (Cluster)、 企业空间 (Workspace)、 项目 (Project) 和 DevOps Project (DevOps 工程)，
       层级关系如下图所示，即一个集群中可以创建多个企业空间，而每个企业空间，可以创建多个项目和 DevOps工程，而集群、企业空间、项目和 DevOps工程中，默认有多个不同的内置角色。
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118150141.png)
    - [DevOps]()
      ```.text
       1.开箱即用的 DevOps：基于 Jenkins 的可视化 CI / CD 流水线编辑，无需对 Jenkins 进行配置，同时内置丰富的 CI/CD 流水线插件
       2.CI/CD 图形化流水线提供邮件通知功能，新增多个执行条件
       3.为流水线、s2i、b2i 提供代码依赖缓存支持
       4.端到端的流水线设置：支持从仓库 (Git/ SVN / BitBucket)、代码编译、镜像制作、镜像安全、推送仓库、版本发布、到定时构建的端到端流水线设置
       5.安全管理：支持代码静态分析扫描以对 DevOps 工程中代码质量进行安全管理
       6.日志：日志完整记录 CI / CD 流水线运行全过程
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118144808.png)
    - [快速构建与发布]()
      ```.text
       1.提供对代码（Source-to-Image）或者制品（Binary-to-Image）进行快速容器化的工具，无需编写 dockerfile，仅需要通过简单的设置即可将制品和代码构建成服务。
       2.支持从已有的代码仓库中获取代码，或通过上传制品的方式，自动构建镜像和完成部署，并将镜像推送至目标仓库，每次构建镜像和服务的过程将以任务 (Job) 的方式去完成。
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118144858.png)
    - [多维度监控]()
      ```.text
       1.KubeSphere 全监控运维功能可通过可视化界面操作，同时，开放标准接口，易于对接企业运维系统，以统一运维入口实现集中化运维
       2.立体化秒级监控：秒级频率、双重维度、十六项指标立体化监控
         2.1 在集群资源维度，提供 CPU 利用率、内存利用率、CPU 平均负载、磁盘使用量、inode 使用率、磁盘吞吐量、IOPS、网卡速率、容器组运行状态、ETCD 监控、API Server 监控等多项指标
         2.2 在应用资源维度，提供针对应用的 CPU 用量、内存用量、容器组数量、网络流出速率、网络流入速率等五项监控指标。并支持按用量排序和自定义时间范围查询，快速定位异常
       3.提供按节点、企业空间、项目等资源用量排行
       4.提供服务组件监控，快速定位组件故障
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118145016.png)
    - [自研多租户告警系统]()
      ```.text
       1.支持基于多租户、多维度的监控指标告警，目前告警策略支持集群管理员对节点级别和租户对工作负载级别等两个层级
       2.灵活的告警策略：可自定义包含多个告警规则的告警策略，并且可以指定通知规则和重复告警的规则
       3.丰富的监控告警指标：提供节点级别和工作负载级别的监控告警指标，包括容器组、CPU、内存、磁盘、网络等多个监控告警指标
       4.灵活的告警规则：可自定义某监控指标的检测周期长度、持续周期次数、告警等级等
       5.灵活的通知发送规则：可自定义发送通知时间段及通知列表，目前支持邮件通知
       6.自定义重复告警规则：支持设置重复告警周期、最大重复次数并和告警级别挂钩
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118145131.png)
    - [日志查询与收集]()
      ```.text
       1.提供多租户日志管理，在 KubeSphere 的日志查询系统中，不同的租户只能看到属于自己的日志信息，支持中文日志检索，支持日志导出
       2.多级别的日志查询 (项目/工作负载/容器组/容器以及关键字)、灵活方便的日志收集配置选项等
       3.支持多种日志收集平台，如 Elasticsearch、Kafka、Fluentd
       4.对于将日志以文件形式保存在 Pod 挂盘上的应用，支持开启落盘日志收集功能
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118145241.png)
    - [应用商店]()
      ```.text
       1.基于开源的 OpenPitrix 提供应用商店和应用仓库服务
       2.支持应用上传、应用审核、应用上架与分类、应用部署，为用户提供应用全生命周期管理功能
       3.用户基于应用模板可以快速便捷地部署一个完整应用的所有服务
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118145340.png)
    - [基础设施管理]()
      ```.text
       1.提供存储类型管理、主机管理和监控、资源配额管理，并且支持镜像仓库管理、权限管理、镜像安全扫描。内置 Harbor 镜像仓库，支持添加 Docker 或私有的 Harbor 镜像仓库。
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118145425.png)
    - [多存储类型支持]()
      ```.text
       1.支持 GlusterFS、CephRBD、NFS 等开源存储方案，支持有状态存储
       2.NeonSAN CSI 插件对接 QingStor NeonSAN，以更低时延、更加弹性、更高性能的存储，满足核心业务需求
       3.QingCloud CSI 插件对接 QingCloud 云平台各种性能的块存储服务
      ```
      ![img](/images/posts/kubesphere/微信截图_20240118145519.png)
    - [多网络方案支持]()
      ```.text
       1.支持 Calico、Flannel 等开源网络方案
       2.开发了适用于物理机部署 Kubernetes 的 负载均衡器插件 Porter
      ```








#### 参考资料
- [kubesphere文档](https://www.kubesphere.io/)
- [部署高可用的 KubeSphere](https://v2-1.docs.kubesphere.io/docs/zh-CN/installation/multi-node/)
- [在Kubernetes安装KubeSphere](https://v2-1.docs.kubesphere.io/docs/zh-CN/installation/install-on-k8s/)
- [kubesphere源码](https://github.com/kubesphere/kubesphere)

