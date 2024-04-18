---
layout: post
title:  TensorFlow | TensorFlow GPU+CUDA+cuDNN环境搭建以及使用
categories: [TensorFlow]
description: TensorFlow GPU+CUDA+cuDNN环境搭建以及使用
keywords: TensorFlow
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 前言 <br>
tensorflow:深度学习框架 -gpu 使用gpu版本 cuda:用来支持gpu进行复杂计算 cudnn:嵌入cuda的一个神经网络加速库文件 nvidia驱动:用来驱动pc的nvidia显卡。




## 准备工作
### 组件及其版本
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
Anaconda（3）+python（3.11）+tensorflow-gpu（2.13）+CUDA（12.4）+cuDNN（8.1）
</p>


## 环境搭建









## Q&A
### CPU和GPU的区别是什么呢？





### 名称解释

|     名称      |                                          解释说明                                           |  备注   | 
|:-----------:|:---------------------------------------------------------------------------------------:|:-----:|
| tensorflow  |                                         深度学习框架                                          |  |
|    cuda     |   CUDA是显卡厂商NVIDIA推出的运算平台。CUDA™是一种由NVIDIA推出的通用并行计算架构，是一种并行计算平台和编程模型，该架构使GPU能够解决复杂的计算问题   |  |
|    cudnn    |   cuDNN的全称为NVIDIA CUDA® Deep Neural Network library，是NVIDIA专门针对深度神经网络中的基础操作而设计基于GPU的加速库。                                    |  |
|  nvidia驱动   |                                     用来驱动pc的nvidia显卡                                     |  |
|  GPU   |       显卡是我们平时说的GPU，现在大多数的电脑使用NVIDIA公司生产的显卡；常见的型号有Tesla V100，GTX950M，GTX1050TI，GTX1080等  |  |
|  CUDA Toolkit   |     CUDA工具包的主要包含了CUDA-C和CUDA-C++编译器、一些科学库和实用程序库、CUDA和library API的代码示例、和一些CUDA开发工具。      |  |
|  NVCC   |                NVCC就是CUDA的编译器,可以从CUDA Toolkit的/bin目录中获取,类似于gcc就是c语言的编译器。                |  |
|     |                                                                                         |  |
|     |                                                                                         |  |



## 参考资料
- [win10超全Tensorflow-gpu安装+CUDA+cuDNN+keras攻](https://blog.csdn.net/qq_42505705/article/details/83478668)
- [Windows10下使用Conda安装TensorFlow-GPU](https://blog.csdn.net/weixin_46846685/article/details/110292801)
- [深度学习——安装CUDA以及CUDNN实现tensorflow的GPU运行](https://blog.csdn.net/weixin_53966032/article/details/126135123)
- [NVIDIA cuDNN](https://developer.nvidia.cn/cudnn?eqid=dbe2c944001c1e51000000036481b7e6)
- [Win10系统搭建最新Anaconda（3）+python（3.8）+tensorflow-gpu（2.7）+CUDA（11.5）+cuDNN（8.1）环境的详细流程](https://blog.csdn.net/Gerald_Jones/article/details/121370247)
- [CUDA GPU](https://developer.nvidia.cn/cuda-gpus)
- [理清GPU、CUDA、CUDA Toolkit、cuDNN关系以及下载安装](https://blog.csdn.net/qq_42406643/article/details/109545766)
- [下载安装CUDA](https://developer.nvidia.com/cuda-downloads?target_os=Windows)



