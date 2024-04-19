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
NVIDIA CUDA® 深度神经网络库 (cuDNN) 是一个 GPU 加速的深度神经网络基元库，能够以高度优化的方式实现标准例程（如前向和反向卷积、池化层、归一化和激活层）。
全球的深度学习研究人员和框架开发者都依赖 cuDNN 来实现高性能 GPU 加速。借助 cuDNN，研究人员和开发者可以专注于训练神经网络及开发软件应用，而不必花时间进行低层级的 GPU 性能调整。
cuDNN 可加速广泛应用的深度学习框架，包括 Caffe2、Chainer、Keras、MATLAB、MxNet、PaddlePaddle、PyTorch 和 TensorFlow。


## 准备工作
### 组件及其版本
- 版本信息确定 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
Anaconda（3）+ Python（3.11.8）+ Tensorflow-gpu（2.13）+ CUDA（11.8.0）+ cuDNN（8.9.2）
</p>

- 查看本机GPU的cuda版本 <br>
  ![img](/images/posts/algorithm/微信截图_20240419094626.png)<br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
  显示CUDA版本为12.4 => cuda(cudatoolkit)版本低于或等于12.4均可（可向下兼容）.
  上图红框为：显存使用/显存大小，蓝框为GPU使用率。
</p>

- 查看NVIDIA控制面板 <br>
  ![img](/images/posts/algorithm/微信截图_20240419095125.png)<br>

## 环境搭建
### Anaconda+Python虚拟环境安装
  ![img](/images/posts/algorithm/微信截图_20240419100022.png)<br>
  ![img](/images/posts/algorithm/微信截图_20240419100125.png)<br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>

### 安装CUDA与cuDNN
- 查看cuDNN可安装版本 <br>
  ![img](/images/posts/algorithm/微信截图_20240419093020.png)<br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>

- 查看CUDA可安装版本 <br>
  ![img](/images/posts/algorithm/微信截图_20240419092855.png)<br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>

#### 命令安装
- cuDNN安装 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
安装命令：conda install cudnn=8.9.2 -c anaconda <br>
在这条命令中，-c anaconda 指定了从 anaconda channel 安装包，cudnn=8.2.1 精确指定了要安装的软件包及其版本号。
</p>

- CUDA安装 <br>
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
安装命令：
</p>

#### 下载安装
- CUDA的下载以及安装 <br>
  ![img](/images/posts/algorithm/微信截图_20240419110600.png)<br>

- cuDNN下载以及安装 <br>
  ![img](/images/posts/algorithm/微信截图_20240419110913.png)<br>



### 安装tensorflow-gpu
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">

</p>


## 测试


  
## Q&A
### CPU和GPU的区别是什么呢？
![img](/images/posts/algorithm/20210602164302297.png)<br>

### cuDNN的主要特性
<p align="left" style="color:grey; font-family:Arial; font-size: 15px">
1. 为各种常用卷积实现了 Tensor Core 加速，包括 2D 卷积、3D 卷积、分组卷积、深度可分离卷积以及包含 NHWC 和 NCHW 输入及输出的扩张卷积 <br>
2. 为诸多计算机视觉和语音模型优化了内核，包括 ResNet、ResNext、EfficientNet、EfficientDet、SSD、MaskRCNN、Unet、VNet、BERT、GPT-2、Tacotron2 和 WaveGlow <br>
3. 支持 FP32、FP16、BF16 和 TF32 浮点格式以及 INT8 和 UINT8 整数格式 <br>
4. 4D 张量的任意维排序、跨步和子区域意味着可轻松集成到任意神经网络实现中 <br>
5. 能为各种 CNN 体系架构上的融合运算提速 <br>
</p>


### 名称解释

|      名称      |                                          解释说明                                          |  备注   | 
|:------------:|:--------------------------------------------------------------------------------------:|:-----:|
|  tensorflow  |                                         深度学习框架                                         |  |
|     cuda     |  CUDA是显卡厂商NVIDIA推出的运算平台。CUDA™是一种由NVIDIA推出的通用并行计算架构，是一种并行计算平台和编程模型，该架构使GPU能够解决复杂的计算问题   |  |
|    cudnn     | cuDNN的全称为NVIDIA CUDA® Deep Neural Network library，是NVIDIA专门针对深度神经网络中的基础操作而设计基于GPU的加速库。 |  |
|  nvidia驱动    |                                     用来驱动pc的nvidia显卡                                    |  |
|     GPU      |    显卡是我们平时说的GPU，现在大多数的电脑使用NVIDIA公司生产的显卡；常见的型号有Tesla V100，GTX950M，GTX1050TI，GTX1080等    |  |
| CUDA Toolkit |     CUDA工具包的主要包含了CUDA-C和CUDA-C++编译器、一些科学库和实用程序库、CUDA和library API的代码示例、和一些CUDA开发工具。     |  |
|     NVCC     |               NVCC就是CUDA的编译器,可以从CUDA Toolkit的/bin目录中获取,类似于gcc就是c语言的编译器。                |  |
|              |                                                                                        |  |
|              |                                                                                        |  |



## 参考资料
- [win10超全Tensorflow-gpu安装+CUDA+cuDNN+keras攻](https://blog.csdn.net/qq_42505705/article/details/83478668)
- [Windows10下使用Conda安装TensorFlow-GPU](https://blog.csdn.net/weixin_46846685/article/details/110292801)
- [深度学习——安装CUDA以及CUDNN实现tensorflow的GPU运行](https://blog.csdn.net/weixin_53966032/article/details/126135123)
- [NVIDIA cuDNN](https://developer.nvidia.cn/cudnn?eqid=dbe2c944001c1e51000000036481b7e6)
- [Win10系统搭建最新Anaconda（3）+python（3.8）+tensorflow-gpu（2.7）+CUDA（11.5）+cuDNN（8.1）环境的详细流程](https://blog.csdn.net/Gerald_Jones/article/details/121370247)
- [CUDA GPU](https://developer.nvidia.cn/cuda-gpus)
- [理清GPU、CUDA、CUDA Toolkit、cuDNN关系以及下载安装](https://blog.csdn.net/qq_42406643/article/details/109545766)
- [下载安装CUDA](https://developer.nvidia.com/cuda-downloads?target_os=Windows)
- [Win11极速安装Tensorflow-gpu+CUDA+cudnn](https://blog.csdn.net/weixin_46713695/article/details/135038527)
- [Tensorflow与Python、CUDA、cuDNN的版本对应表](https://blog.csdn.net/ly869915532/article/details/124542362)
- [Windows安装GPU版本的tensorflow+CUDA+CUDNN（超详细）](https://blog.csdn.net/m0_46093829/article/details/122893287/)
- [搭建CUDA11.8.0+cuDNN8.9.2+python3.11.4+anaconda23.5.2环境的过程记录](https://blog.csdn.net/mugua_JC/article/details/131849295)
- [CUDA下载链接](https://developer.nvidia.cn/cuda-downloads)
- [cuDNN下载链接](https://developer.nvidia.cn/cudnn)
- [cuda-toolkit-archive](https://developer.nvidia.com/cuda-toolkit-archive)
- [cudnn-downloads](https://developer.nvidia.com/cudnn-downloads)
- [python&anconda系列（亲测有效）：window11系统CUDA、cuDNN 安装以及环境变量配置](https://blog.csdn.net/weixin_54626591/article/details/135183720)
- [在win11使用4070s显卡作为GPU 配置pytorch](https://blog.csdn.net/qq_37344058/article/details/135964731)
- [NVIDIA开发者社区](https://developer.nvidia.cn/)