---
layout: post
title:  Flink | Lakehouse技术架构整理
categories: [data lake]
description: Lakehouse技术架构整理
keywords: Paimon
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 前言 <br>
旧的数据分析架构如 Hive、Hadoop、HDFS、MapReduce、HiveSQL、Hive 存储等，如今国内外的各大企业都在逐步转向 Lakehouse 架构，即 Spark、Flink、Presto，底层的湖存储格式：Iceberg、Delta、Hudi、Paimon，以及下面数据存储在 HDFS、对象存储 OSS 或 S3。


## 背景需求

## 数据湖
### Lakehouse架构的优势
- 实现了计算存储分离 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
旧的架构 Hadoop 计算存储都在一个集群中，若要扩容，就要计算与存储部分同时扩容，但目前，各行各业都会有庞大的数据，却不一定可以匹配足够的计算，行业现状催生了计算存储分离的需求。
如此，可以实现存储变大的同时，计算资源基本维持不变，当落到 OSS 对象存储上之后，计算存储分离变得非常简单。
</p>

- 实现了存储冷热分层 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
这是对象存储带来的独特优势。对于对象存储，我们可以利用它的冷存，因为其价格相对低廉，但其冷存访问成本会上升，因此，不常使用的数据可以使用冷存，从而大大降低成本。
</p>

- 操作更加灵活 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
前面提到的两点实际上 Hive 存储也可以实现，但相较而言，Lakehouse 操作更加灵活，因为湖存储格式提供了更多的 ACID，包括 DELETE、UPDATE 之类的语法，可以让数仓的操作更加方便，而不像 Hive 只能 INSERT OVERWRITE。
</p>

- 查询速度更快 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
因为湖存储带来的 Meta 上的 skipping 可使得数据根据 Filter 条件做出更多的下推，查询性能更高。
</p>

- 时效性到分钟级 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
或许前面四点对许多企业来说没有足够的吸引力，而且旧的 Hive 非常稳定，这可能会导致企业数据分析架构的迁移动力大大降低。但是，除前面提到的四点之外，Lakehouse 架构真正给业务带来价值的是可以使得时效性从 T+1 降低到分钟级。
在这个方面，Flink/Spark 是当之无愧的专家。真正能打动企业迁移湖仓架构的关键是，能否将 Flink/Spark 融入湖仓架构中。
</p>

### 常见Lakehouse架构
![img](/images/posts/bigdata/lakehouse/微信截图_20240416170314.png)<br>












## 附录




### 名称解释

|     名称     |                                         解释说明                                          |  备注   | 
|:----------:|:-------------------------------------------------------------------------------------:|:-----:|
|     |                                                                              |  |





## 参考资料











