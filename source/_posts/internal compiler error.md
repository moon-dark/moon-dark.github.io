---
title: '解决: g++: internal compiler error: Killed (program cc1plus)'
tags:
  - System
  - 编译
  - code
date: 2017-04-30 12:26:48
---

g++: internal compiler error: Killed (program cc1plus)

主要原因是内存不足，g++编译时需要大量内存， 临时使用交换分区来解决吧
<!--more-->
```Bash
sudo dd if=/dev/zero of=/swapfile bs=64M count=16
sudo mkswap /swapfile
sudo swapon /swapfile
```

After compiling, you may wish to

Code:
```
sudo swapoff /swapfile
sudo rm /swapfile
```
