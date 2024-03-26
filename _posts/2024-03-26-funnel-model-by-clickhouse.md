---
layout: post
title:  Data Analysis | 基于ClickHouse的漏斗模型实践
categories: [data analysis、clickhouse]
description: 基于ClickHouse的漏斗模型实践
keywords: clickhouse、sql
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 背景 <br>
日常工作中做为数仓开发工程师、数据分析师经常碰到漏斗分析模型，本文详细介绍漏斗模型的概念及基本原理，并阐述了其在平台内部的具体实现。针对实际使用过程的问题，探索基于 ClickHouse漏斗模型实践方案。

## 背景需求
```.text
漏斗分析是衡量转化效果、进行转化分析的重要工具，是一种常见的流程式的数据分析方法。它能够帮助你清晰地了解转化情况，从多角度剖析对比，定位流失原因，提升转化表现。
它主要立足于三大需求场景：
1.定位用户流失具体原因。
2.检测某个专题活动效果。
3.针对不同版本，转化率情况对比。
```

## 概述
- [概念介绍]()
```.text
漏斗模型主要用于分析一个多步骤过程中每一步的转化与流失情况。其中有几个概念要了解：
```
![img](/images/posts/analysis/微信截图_20240326105645.png)<br>
```.text
其中漏斗模型分为两种：无序漏斗和有序漏斗。
定义如下：
无序漏斗：在漏斗的周期内，不限定漏斗多个步骤之间事件发生的顺序。
【计算规则】：假设一个漏斗中包含了 A、B、C 3个步骤，A步骤发生的时间可以在B步骤之前，也可以在B的后面。用户的行为顺序为A、B、C的组合都算成功的漏斗转化。即使漏斗步骤之间穿插一些其他事件步骤，依然视作该用户完成一次成功的漏斗转化。
有序漏斗：在漏斗的周期内，严格限定漏斗每个步骤之间的发生顺序。
【计算规则】：假设一个漏斗中包含了 A、B、C 3个步骤，A步骤发生的时间必须在B步骤之前，用户的行为顺序必须为A->B->C 。和无序漏斗一样，漏斗步骤之间穿插一些其他事件步骤，依然视作该用户完成一次成功的漏斗转化。
```


##  用漏斗进行的数据分析
```.text
了解了上面的关于漏斗模型的基本概念，我们看一下如何创建一个漏斗。
```
- [选一个漏斗类型]()
```.text
漏斗模型的类型一般分为有序漏斗和无序漏斗，它们的概念已在2.1做了详细的介绍。我们这里以无序漏斗为例，创建漏斗模型。
```
- [添加漏斗步骤]()
```.text
漏斗步骤就是漏斗分析的核心部分，步骤间统计数据的对比，就是我们分析步骤间数据的转化和流失的关键指标。
比如我们以一个“下载应用领红包”的活动为例。预设的用户的行为路径是：用户首先进入【红包首页】，发现最新的红包活动“下载应用，领取红包”，点击进入【红包活动页】，
根据提示跳转到【应用下载页】，选择自己感兴趣的应用下载，完成后，进入【提现页面】领取活动奖励。从上面描述的场景中，我们可以提取出以下关键的四步。
```
![img](/images/posts/analysis/微信截图_20240326110006.png)<br>
- [确定漏斗的时间区间和周期]()
```.text
这里多了一个时间区间的概念，与前文介绍的周期容易混淆。一般来说，此类数据的数仓表是按照时间分区的。所以选择时间区间，本质就是选择要计算的数据范围。
周期是指一个漏斗从第一步流转到最后一步的时间限制，即是用来界定怎样才是一个完整的漏斗。在本例中，我们按照天为周期进行处理，选择时间区间为“2021-05-27”、“2021-05-28”、“2021-05-29”。
```
- [漏斗数据的展示]()
```.text
依据我们设计的漏斗模型（具体模型设计，下文会提及），可以计算出下表的数据：
```
![img](/images/posts/analysis/微信截图_20240326110149.png)<br>


## 实际案例分享
### 多线程操作 HashMap 线程死循环
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
从死循环信息来看：
1.两个线程堆栈均为高 CPU 线程，且最后都是在操作 HashMap。很明显是踩到了 HashMap 多线程不安全的这个坑。
2.将对应 HashMap 的使用改为 ConcurrentHashMap 之后，该问题得到解决。
```
### Lottie 动画后台未停止导致高 CPU 消耗
ANRCanary 抓到的上报信息如下：
```.json
"case:-1056518995":{
  "name":"main",
  "threadCPURate":0.*,
  "threadStackList":[
        "com.airbnb.lottie.LottieAnimationView.invalidateDrawable(SourceFile:189)",
    "com.airbnb.lottie.LottieDrawable.invalidateSelf(SourceFile:261)",
    "lb.onValueChanged(SourceFile:100)",
        "com.airbnb.lottie.animation.keyframe.BaseKeyframeAnimation.b(SourceFile:60)",
    "nj.a(SourceFile:427)",
      "com.airbnb.lottie.LottieDrawable$1.onAnimationUpdate(SourceFile:103)",
    "pb.c(SourceFile:88)",
    "pd.doFrame(SourceFile:96)",
    "android.view.Choreographer$CallbackRecord.run(Choreographer.java:785)",
    "android.view.Choreographer.doFrame(Choreographer.java:568)",
    "android.os.Handler.dispatchMessage(Handler.java:95)",
    "android.os.Looper.loop(Looper.java:136)",
    "android.app.ActivityThread.main(ActivityThread.java:5336)",
    "com.android.internal.os.ZygoteInit.main(ZygoteInit.java:689)"
  ]
}
从死循环信息来看：
1.发生死循环的是主线程。
2.主线程相对于整个 APP 进程，CPU 占用量很高。
3.从堆栈看是因为 Lottie 动画导致的。
4.经过本地验证发现确实存在切换到后台 Lottie 依然在执行动画的问题。
5.原因是因为钉钉使用的 Lottie 版本太低导致，升级 Lottie 版本之后该问题得到解决。
```
### 属性动画泄露导致高 CPU 消耗
ANRCanary 抓到的上报信息如下：
```.json
"case:-647468375":{
  "name":"main",
  "threadCPURate":0.*,
  "threadStackList":[
    "android.graphics.drawable.LayerDrawable.setAlpha(LayerDrawable.java:1364)",
    "android.animation.ObjectAnimator.animateValue(ObjectAnimator.java:990)",
    "android.animation.ValueAnimator.animateBasedOnTime(ValueAnimator.java:1349)",
    "android.animation.ValueAnimator.doAnimationFrame(ValueAnimator.java:1481)",
    "android.animation.AnimationHandler.doAnimationFrame(AnimationHandler.java:146)",
    "android.animation.AnimationHandler$1.doFrame(AnimationHandler.java:54)",
    "android.view.Choreographer.doCallbacks(Choreographer.java:1047)",
    "android.view.Choreographer.doFrame(Choreographer.java:914)",
    "android.os.Handler.dispatchMessage(Handler.java:100)",
    "android.os.Looper.loop(Looper.java:214)",
    "android.app.ActivityThread.main(ActivityThread.java:7659)",
    "com.android.internal.os.ZygoteInit.main(ZygoteInit.java:938)"
  ]
}
从死循环信息来看：
1.死循环发生在主线程。
2.主线程相对于整个进程，CPU 占用率非常高，大量抢占了子线程的 CPU 时间片。
3.堆栈里没有业务堆栈，因此暂时无法定位到问题代码。只能看出是发生了属性动画泄露，需要增加监控能力。
监控能力增强->ANRCanary 在感知到发生属性动画泄露以后，需要将发生泄露的属性动画揪出来。
大致方案为：
1.从系统 AnimationHandler 类入手，获取到当前运行中的属性动画实例列表。
2.从属性动画实例中提取关键信息，并附加到 ANRCanary 日志中上报。
能力增强后的 ANRCanary 抓到的上报信息如下：
"case:-647468375":{
  "attachInfo":{
    "animatorList":[
      {
        "duration":1200,
        "propertyList":[
          {
            "clazz":"IntPropertyValuesHolder",
            "message":"alpha:  25  255  "
          }
        ],
        "repeatCount":-1,
        "target":"android.graphics.drawable.LayerDrawable",
              "viewPath":"TextView:recording|RelativeLayout:0|RelativeLayout:0"
      },
      ...
  },
  "name":"main",
  "threadCPURate":0.*,
  "threadStackList":[
    "android.graphics.drawable.LayerDrawable.setAlpha(LayerDrawable.java:1364)",
    "android.animation.ObjectAnimator.animateValue(ObjectAnimator.java:990)",
    "android.animation.ValueAnimator.animateBasedOnTime(ValueAnimator.java:1349)",
    "android.animation.ValueAnimator.doAnimationFrame(ValueAnimator.java:1481)",
    "android.animation.AnimationHandler.doAnimationFrame(AnimationHandler.java:146)",
    "android.animation.AnimationHandler$1.doFrame(AnimationHandler.java:54)",
    "android.view.Choreographer.doCallbacks(Choreographer.java:1047)",
    "android.view.Choreographer.doFrame(Choreographer.java:914)",
    "android.os.Handler.dispatchMessage(Handler.java:100)",
    "android.os.Looper.loop(Looper.java:214)",
    "android.app.ActivityThread.main(ActivityThread.java:7659)",
    "com.android.internal.os.ZygoteInit.main(ZygoteInit.java:938)"
  ]
}
从附加信息来看，泄露的属性动画为：
1.duration：该动画时长为 1200 毫秒。
2.propertyList：
 2.1 clazz：属性值的类型为 int 。
 2.2 message：属性名为 alpha，属性变化值为从 25 到 255。
3.repeatCount：循环次数为 -1 ，即无限循环永远不停止，难怪会发生泄露。
4.target：alpha 属性所属的对象类型为：LayerDrawable 。
5.viewPath：
  5.1 LayerDrawable 所属的 View 类型为 TextView。
  5.2 该 TextView 的 viewId 为 recording 。
  5.3 该 TextView 的父辈节点均为 RelativeLayout 类型。
基于 viewId 、动画时长、属性变化值等信息，快速定位到问题代码，确认了导致泄露的原因并修复。    
```
### 定时任务执行异常导致死循环
本案例为一个隐蔽且复杂的死循环问题，解决过程极具挑战性，耗时甚长。
ANRCanary 抓到的上报信息如下：
```.json
"case:-2147483648":{
  "name":"Timer-0",
  "threadCPURate":0.*,
  "threadStackList":[
    "android.os.MessageQueue.enqueueMessage(MessageQueue.java:577)",
    "android.os.Handler.enqueueMessage(Handler.java:662)",
    "android.os.Handler.sendMessageAtTime(Handler.java:631)",
    "android.os.Handler.sendMessageDelayed(Handler.java:601)",
    "android.os.Handler.postDelayed(Handler.java:429)",
    "de.executor(SourceFile:31)",
    "tm.query(SourceFile:268)",
    "mk.start(SourceFile:166)",
    "mk$1.run(SourceFile:93)",
    "java.util.TimerThread.mainLoop(Timer.java:555)",
    "java.util.TimerThread.run(Timer.java:505)"
  ]
}
从死循环信息来看：
1.发生死循环的线程名为：Timer-0 。
2.该线程相对于整个进程，CPU 占用率也很高。
3.死循环的原因看起来是某个消息队列被打满导致。
  3.1 因为 Handler 的消息队列被打满之后，每次 postDelayed 调用都要执行一次插入排序遍历整个队列。
监控能力增强->经过翻阅代码，确认代码中有一个周期性任务。周期性任务的实现方式采用的是 Java 提供的 Timer 类。正常情况下应该不会导致消息队列被打满才对。
ANRCanary 在感知到消息队列疑似被打满以后，需要收集更多信息来进行确认情况。
大致方案为：  
```
![img](/images/posts/thread/25rqeahlh.jpg)<br>
```.text
获取当前进程全部的线程对象，逐个遍历。
如果线程为 HandlerThread 类及其子类，则包含有消息队列，可以尝试获取其消息队列长度。
如果消息队列长度超过一定阈值，则可以判定为消息队列被打满。
对消息队列中的消息进行遍历聚合，分析出 Top 级消息内容。
允许存在多个线程的消息队列被打满的情况。
```
能力增强后的 ANRCanary 抓到的上报信息如下：<br>
```.json
"case:-2147483648":{
  "attachInfo":{
    "messageQueueList":[
      {
        "repeatRate":1,
        "repeatSignature":"android.os.Handler|jlh",
        "threadName":"TaskHandlerThread",
        "totalCount":****
      }
    ]
  },
  "name":"Timer-0",
  "threadCPURate":0.*,
  "runTime":***,
  "threadStackList":[
    "android.os.MessageQueue.enqueueMessage(MessageQueue.java:577)",
    "android.os.Handler.enqueueMessage(Handler.java:662)",
    "android.os.Handler.sendMessageAtTime(Handler.java:631)",
    "android.os.Handler.sendMessageDelayed(Handler.java:601)",
    "android.os.Handler.postDelayed(Handler.java:429)",
    "de.executor(SourceFile:31)",
    "tm.query(SourceFile:268)",
    "mk.start(SourceFile:166)",
    "mk$1.run(SourceFile:93)",
    "java.util.TimerThread.mainLoop(Timer.java:555)",
    "java.util.TimerThread.run(Timer.java:505)"
  ]
}
从附加信息来看：
repeatRate：消息队列重复率为 100%，说明均为同一类消息。
repeatSignature：重复消息的 Runnable 类型混淆后为：jlh，确实为监控模块的周期性任务。
threadName：消息队列所属的线程名为：TaskHandlerThread
totalCount：消息队列长度远远超出正常消息队列的长度，确实被打满。
runTime：进程存活时长并不大。正常消息队列应该只有几条消息，而不应该被打满。
看来 Timer 的间隔能力确实失效了，死循环问题是真实存在的。
最终定位:对于 Timer 的运行机制进行深入分析以后发现问题：
```
```.java
private void mainLoop() {
  while (true) {
    TimerTask task;
    boolean taskFired;
    long currentTime, executionTime;
    // 1.从运行队列中获取头部的周期性任务
    task = queue.getMin();
    // 2.获取系统时间（关键点！！！）
    currentTime = System.currentTimeMillis();
    // 3.获取周期性任务期望执行时间
    executionTime = task.nextExecutionTime;
    taskFired = executionTime <= currentTime;
    // 4.用两个时间的比较结果，决定是否执行周期性任务
    if (taskFired) {
      task.run();
    }
  }
}
```
```.text
如以上代码片段第9行所示，Timer 的间隔时间是基于系统时间戳的。也就是说，修改手机系统时间会影响 Timer 周期性任务的执行。
写一个简单的 Demo 验证以后得出结论：
1.如果往前修改系统时间，周期性任务将停止执行。
2.如果往后修改系统时间，间隔时间会失效，周期性任务开始连续不停地执行。
至此终于定位到该死循环的根因。修改方案就是把 Timer 替换为 ScheduleExecutorService 来实现周期性任务，将不受系统时间变更的影响。
```




## 参考资料
- [多线程详解](https://blog.csdn.net/weixin_36490770/article/details/128189662)




