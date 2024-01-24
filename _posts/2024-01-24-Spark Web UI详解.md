---
layout:     post
title:      Spark Web UI
subtitle:   Spark Web UI详解
date:       2024-01-24
author:     Tony
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - Bigdata
---

<h4 align="left"> Spark Web UI整理 </h4>

---------------

- [Jobs]()
  ```.text
   User：hadoop账号，和登录hope时需要的User相同
   Total Uptime：spark作业的执行时间，完成的作业=结束时间➖开始时间，进行中的作业该值会随着刷新在改变。该时间不是所有job的执行时间之和（有并行的job，job与job之间也有时间间隔），
   也不是最后一个job的结束时间➖第一个job的提交时间（作业启动到job提交之前还有一些时间）
   Scheduling Model：Job的调度模式，分为FIFO（先进先出）和FAIR（公平调度）。可以并行的Job，FIFO是谁先提交谁先执行可以理解为JobId小的先执行。FAIR会根据权重决定哪个Job先执行
  ```

- [Stages]()


- [Storage]()


- [Environment]()


- [SQL]()


- [Debug]()



