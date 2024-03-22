---
layout: post
title: Thread | 端线程死循环的治理
categories: [Thread]
description: 端线程死循环的治理
keywords: Thread、Java
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 背景
Q: 线程死循环问题可能会导致高耗电，界面卡顿，耗尽内存等性能问题。与此同时，死循环问题还具有隐蔽性高，黑盒测试无法感知的特点。<br>
A: 本文旨在介绍死循环检测工具建设的思路和典型案例的修复历程。希望通过此次分享，对同样面临类似死循环问题的团队能够有所启发。<br>

## 线程死循环检测机制
```.text
线程死循环检测是一项复杂的任务。死循环可以在代码的任何地方发生，从简单的逻辑错误到复杂的系统原因，均可能会导致死循环。因此检测并诊断此类问题相当困难。
ANRCanary 监控工具，基于死循环线程高 CPU 占用和堆栈相似的两个特点，实现了精准的死循环检测能力。
```
- [获取线程CPU占用比]()
  ```.text
   通过读取 Android 系统 proc 目录下面的 stat 文件可以获取进程和线程的 CPU 耗时，如下所示：
  ```
  - 进程的stat信息
    ![img](/images/posts/thread/微信截图_20240322092424.png)<br>
    ```.text
     进程 ID 为：11162
     进程用户态 CPU 耗时 101， 内核态 CPU 耗时 51 。
     对于死循环来说，CPU 的占用主要体现在用户态，所以我们只关注用户态的 CPU 占用比就好。
    ```
  - 主线程的stat信息
    ![img](/images/posts/thread/微信截图_20240322092646.png)<br>
    ```.text
     主线程的线程 ID 为 11162， 与进程 ID 相同。
     主线程用户态 CPU 耗时 12， 内核态 CPU 耗时 8 。则主线程用户态 CPU 占用比为：12 / 101 = 12%
    ```
  - 子线程Jit thread pool的stat信息
    ![img](/images/posts/thread/微信截图_20240322092808.png)<br>
    ```.text
     子线程的线程 ID 为：11168，与进程 ID 不相同。
     子线程用户态和内核态 CPU 耗时均为 0 ，则该子线程用户态 CPU 占用比为：0% 。
    ```
    ```.text
     如果想计算一段时间区域内的各线程 CPU 占用比，只需要根据进程和线程的 CPU 耗时差值，计算即可得出。
     通常可以将 CPU 占用比超过 10% 的线程标记为高耗时线程。
    ```

## 堆栈比较方案
```.text
死循环线程还有一个特点就是当出现一个循环点时，线程堆栈的底部是永远相同的。
以查询历史数据过多的数据库而导致死循环的案例堆栈为例：
```
![img](/images/posts/thread/微信截图_20240322093125.png)<br>
```.text
如上图所示，针对该线程在一定时间间隔内抓的三个堆栈，可以发现堆栈的相似度很高，则可以将其作为死循环的重点怀疑对象。
```

## CPU占用和堆栈比较相结合
### 起因
```.text
CPU 占用方案：基于 stat 文件可以知道一个线程占了比较高的 CPU ，但是有效信息只有线程名， 对应不上代码，因此很难分析出耗 CPU 的根因。
堆栈比较方案：能够拿到堆栈信息，但是当线程发生阻塞时（比如读 IO 或跨进程通信），堆栈相似度也很高，因此误报率比较高。
因此唯有将两个方案结合起来，才能比较准确地拿到高 CPU 占用线程的堆栈信息，从而大大提高检测结果的可分析性。
```
- [结合方案]()
  ```.text
  结合的关键点为：线程名。因为两个方案输出的信息均包含线程名，因此可以用线程名进行关联匹配。
  ```
  ![img](/images/posts/thread/thread112.jpg)<br>
  ```.text
   如上图所示，完整版本的死循环线程检测机制详细说明如下：
  ```
  > 首先是线程 CPU 占用比获取阶段  <br>
  > 先基于 stat 文件获取进程和线程的 CPU 耗时。 <br>
  等待一段时间后，再次抓取进程和线程 CPU 耗时。 <br>
  计算所有线程的 CPU 耗时，超过阈值，则认为属于高 CPU 线程。如果没有这类线程，则结束检测流程。<br>
  保存所有的高 CPU 线程的相关信息，留给下一个阶段使用。<br>
  > 其次是堆栈比较阶段  <br>
  > 获取所有的 Java 线程对象列表，并进行遍历。 <br>
  > 先对 Java 线程进行高 CPU 线程名匹配，如果匹配不中，不会进行堆栈比较，直接移除。 <br>
  > 在一定时间内，针对剩余线程连续抓几次堆栈进行堆栈比较，堆栈相似度低于阈值的线程会被移除。  <br>
  > 最后是输出检测结果通过筛选的线程就是疑似发生死循环的线程。<br>
  > 将 CPU 占用比信息与线程堆栈信息合并之后，输出检测结果。 <br>

## 实际案例分享
### 案例 1：多线程操作 HashMap 线程死循环
本案例涉及 Java 中一个经典的死循环问题，即在多线程环境中操作 HashMap 有可能触发无限循环的情况。
ANRCanary 抓到的上报信息如下：<br>
```.json
{
  "case:1420548922":{
    "name":"DThread-2",
    "threadCPURate":0.*,
    "threadStackList":[
      "java.util.HashMap.put(HashMap.java:425)",
      "fde.f(SourceFile:299)",
      "iwx$1.doAfter(SourceFile:57)",
      "prw.parseData(SourceFile:23)",
      "prv.a(SourceFile:18)",
      "pru$1.run(SourceFile:56)",
      "com.***.threadpool.TaskRunner.call(SourceFile:750)",
      "java.lang.Thread.run(Thread.java:762)"
    ]
  },
  "case:1961205280":{
    "name":"DThread-19",
    "threadCPURate":0.*,
    "threadStackList":[
      "java.util.HashMap.put(HashMap.java:425)",
      "fde.f(SourceFile:299)",
      "iwx$1.doAfter(SourceFile:57)",
      "prw.parseData(SourceFile:23)",
      "psc.a(SourceFile:342)",
      "com.***.im.cl.a(SourceFile:100)",
      "prz.query(SourceFile:2628)",
      "psh$12.onExecuteRpc(SourceFile:244)",
      "pri$1.run(SourceFile:143)",
      "com.***.threadpool.TaskRunner.call(SourceFile:750)",
      "java.lang.Thread.run(Thread.java:762)"
    ]
  }
}
```
### 案例 2：Lottie 动画后台未停止导致高 CPU 消耗

### 案例 3：属性动画泄露导致高 CPU 消耗

### 案例 4：定时任务执行异常导致死循环




#### 参考资料

