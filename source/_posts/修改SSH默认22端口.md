---
title: 修改SSH默认22端口
tags:
  - System
  - SSH
  - code
date: 2017-06-05 17:49:31
---

首先修改配置文件
```vi /etc/ssh/sshd_config```

找到#Port 22一段，这里是标识默认使用22端口，修改为如下：
<!--more-->
```
Port 22
Port 50000
```
然后保存退出

执行```/etc/init.d/sshd restart```
这样SSH端口将同时工作与22和50000上。

现在编辑防火墙配置：```vi /etc/sysconfig/iptables```

启用50000端口。
执行```/etc/init.d/iptables restart```
