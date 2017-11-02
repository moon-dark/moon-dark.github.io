---
title: SELinux配置
tags:
  - SELinux
  - 系统
  - Tools
  - code
date: 2017-03-23 11:45:21
---

1、查看selinux状态
查看selinux的详细状态，如果为enable则表示为开启
```
# /usr/sbin/sestatus -v
```
查看selinux的模式
```
# getenforce
```

2、关闭selinux
2.1:永久性关闭（这样需要重启服务器后生效）
```
# sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
```
2.2:临时性关闭（立即生效，但是重启服务器后失效）
```
#设置selinux为permissive模式（即关闭）
# setenforce 0
#设置selinux为enforcing模式（即开启）
# setenforce 1
```
