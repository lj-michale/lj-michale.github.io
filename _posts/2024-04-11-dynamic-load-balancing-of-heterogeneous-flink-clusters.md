---
layout: post
title:  Flink | Flink多线程实现异构集群的动态负载均衡
categories: [Flink]
description: Flink多线程实现异构集群的动态负载均衡
keywords: Flink
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 前言 <br>
在实时计算应用场景中经常会有对异构集群的实时调用需求，而当异构集群的服务由于机器配置、节点负载等原因无法做到负载均衡时，可以通过Flink的自定义多线程来实现对异构集群的动态负载均衡。


## 背景需求
```.text
文本内容鉴别、图片内容鉴别、图片OCR等特征生产需求，都需要和基于GPU部署的异构集群来交互。如果GPU集群机器配置无法统一，那么就会产生负载不均的情况。
即：一个GPU集群中某些节点处理的快，某些节点处理的慢，处理慢的节点往往会导致大量的超时异常，从而引起整个作业的反压。
其流程图如下：
我们借助Flink分布式的先天优势，在任务中通过Thrift RPC调用模型服务，实时获取结果后再写到特征工程，以此来构建特征生成整个链路。
```
![img](/images/posts/bigdata/flink/微信截图_20240411175913.png)<br>
```.text

```





## 参考资料
