---
title: 查看android手机给应用分配的内存
tags:
  - Android
  - 内存
  - code
date: 2017-02-21 11:42:23
---

查看android手机给应用分配的内存：

```
$ adb shell getprop | grep heap
[dalvik.vm.heapgrowthlimit]: [192m]
[dalvik.vm.heapmaxfree]: [8m]
[dalvik.vm.heapminfree]: [4m]
[dalvik.vm.heapsize]: [512m]
[dalvik.vm.heapstartsize]: [16m]
[dalvik.vm.heaptargetutilization]: [0.75]
```
<!--more-->
```
dalvik与GC相关的属性有：
dalvik.vm.heapstartsize：初始化dalvik分配的内存大小。
dalvik.vm.heapgrowthlimit：没有在mainfest中设置Android:largeheap="true"时，应用的最大内存，超过这个值会有OOM产生。
dalvik.vm.heapsize：在mainfest中设置android:largeheap="true"时，应用的最大内存，超过这个值会有OOM产生。
dalvik.vm.heaputilization、dalvik.vm.heapminfree 、dalvik.vm.heapmaxfree：dalvik GC时使用的参数。
```
