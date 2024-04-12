---
layout: post
title: Tony | 文档撰写模板
categories: [Markdown]
description: Markdown文档撰写模板范例
keywords: Java, Markdown, Flink, Tony
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

<h3 align="left"> Markdown文档撰写范例模板 </h3>

<hr style="height:3px;border:none;border-top:1px solid #0066CC;" />


<h4 align="left"> 前言 </h4>

---------------

- [背景]()


<h4 align="left"> 正文 </h4>

---------------

- [markdown语法]()
  - [横线]() <br>
    <hr style=" height:2px;border:none;border-top:2px dotted #185598;" /> 
    <!-- 
      类型：园点虚线
      height:2px;是hr的高度
      border:none;是没有边框
      border-top:2px dotted #185598;是设置横线的样式
      dotted  虚线  #185598  颜色
    -->
    <hr style="height:1px;border:none;border-top:1px dashed #0066CC;" />
    <hr style="height:1px;border:none;border-top:1px solid #555555;" />
    <hr style="height:3px;border:none;border-top:3px double red;" />
    <hr style="height:5px;border:none;border-top:5px ridge green;" />
    <hr style="height:10px;border:none;border-top:10px groove skyblue;" />
    
  - [字体]() <br>
    <font face="黑体">黑体字</font> 
    <font face="微软雅黑">微软雅黑</font> 
    <font face="STCAIYUN">华文彩云</font>
    <font color=blue>蓝色</font>
    <font color=Red>红色</font>
    <font size=5>尺寸</font> 
    <font face="黑体" color=green size=5>黑体，绿色，尺寸为5</font> 
    - [放大加粗字体]() <br>
      <h1>想要放大的字体</h1> 
      <h2>想要放大的字体</h2> 
      <h3>想要放大的字体</h3>
      <h4>想要放大的字体</h4> 
      <h5>想要放大的字体</h5>

  - [强调]() <br>
    **凡有所学，皆成性格。** <br>
    __凡有所学，皆成性格。__*凡有所学，皆成性格。* <br>
    _凡有所学，皆成性格。_ <br>

  - [图片]() <br>
    ![img](/images/posts/bigdata/spark/2020092111554018.png) <br>
    <img src="/images/posts/windows/rclick.png" alt="Windows Skills" /> <br>
    ![img](/images/posts/cplusplus/staticstring_helloworld.png) <br>

    
  - [文本]()
    ```.text
     文本块内容
    ```



<h4 align="left"> 结论 </h4>

---------------

- [文本段落]()
***
<p align="left">
面对大流量请求，如果控制面没有自动弹性扩容能力，会无法对负载自适应、导致控制面服务不可用。<br>
例如：客户端存在高频度持续 LIST 集群中的大量资源，集群 apiserver/etcd 无法自动弹性就可能联动出现OOM。<br>
ACK Pro托管版K8s可以对控制面组件根据负载压力做HPA和VPA，可以有效解决该痛点。
</p>

- [文本居中]()
***
<center>
文本居中内容...
</center>

- [数学公式]()
***
$f(x)=sinx$ <br>








<h5 align="left"> 参考资料 </h5>

- [Markdown横线](https://www.jianshu.com/p/e74830235087)






