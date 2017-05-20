---
layout: posts
title:  "command jmap usage"
date:   2015-09-01 08:00
categories: jmap jvm cmd
permalink: /tech/command-jmap-usage
---

先上点资料，温习一下
JVM的参数列表： http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html

内存相关参数：

```

JVM参数	含义
-Xms	初始堆大小(heap=new+old，不包含perm)
-Xmx	最大堆大小
-Xmn	新生代大小，效果和NewSize/MaxNewSize一样
-XX:NewSize	新生代初始值
-XX:MaxNewSize	新生代最大值
-XX:PermSize	永久代初始值
-XX:MaxPermSize	永久代最大值
-XX:NewRatio	Old:New比例,默认2,优先级低于MaxNewSize和Xmn
-XX:SurvivorRatio	Eden:Survivor比例，默认8

```

如果这么设置

```bash

$ export JAVA_OPTS="-Xms1000m -Xmx1000m -Xmn400m -XX:PermSize=200m -XX:MaxPermSize=200m"

```

让我们计算一下Survivor/Eden/New/Old/Perm大小，以及整个jvm进程占用的内存大小，并查看验证：
* survivor: 根据SurvivorRatio=8, survivor有2块，每块的大小是Xmn*1/(2+8)=40m
* eden：同上，eden大小是 Xmn*8/(2+8)=320m
* new:显式设置400m
* old: heap-new=1000-400=600m
* perm: 显式设置200m

整个进程占用内存： 堆+永久代+线程栈+共享库… 略大于1200m

```bash

$ tomcat/startup.sh

$ jmap -heap `pgrep java` 2>/dev/null | tail +6
Heap Configuration:
   MinHeapFreeRatio = 40
   MaxHeapFreeRatio = 70
   MaxHeapSize      = 1048576000 (1000.0MB)  ### Heap
   NewSize          = 419430400 (400.0MB)
   MaxNewSize       = 419430400 (400.0MB)    ### New
   OldSize          = 5439488 (5.1875MB)
   NewRatio         = 2
   SurvivorRatio    = 8
   PermSize         = 209715200 (200.0MB)
   MaxPermSize      = 209715200 (200.0MB)    ### Perm

Heap Usage:
New Generation (Eden + 1 Survivor Space):
   capacity = 377487360 (360.0MB)
   used     = 120937896 (115.33536529541016MB)
   free     = 256549464 (244.66463470458984MB)
   32.037601470947266% used
Eden Space:
   capacity = 335544320 (320.0MB)            ### Eden
   used     = 120937896 (115.33536529541016MB)
   free     = 214606424 (204.66463470458984MB)
   36.042301654815674% used
From Space:
   capacity = 41943040 (40.0MB)              ### Survivor From
   used     = 0 (0.0MB)
   free     = 41943040 (40.0MB)
   0.0% used
To Space:
   capacity = 41943040 (40.0MB)              ### Survivor To
   used     = 0 (0.0MB)
   free     = 41943040 (40.0MB)
   0.0% used
concurrent mark-sweep generation:
   capacity = 629145600 (600.0MB)            ### Old
   used     = 0 (0.0MB)
   free     = 629145600 (600.0MB)
   0.0% used
Perm Generation:
   capacity = 209715200 (200.0MB)
   used     = 17160872 (16.365882873535156MB)
   free     = 192554328 (183.63411712646484MB)
   8.182941436767578% used

```

约123M
```bash

$ ps aux | grep java | grep -v grep| awk '{print $6}'
123256

```
