---
layout: post
title: Thread | 线程死循环的治理
categories: [Thread]
description: 线程死循环的治理
keywords: Thread、Java
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 背景 <br>
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




