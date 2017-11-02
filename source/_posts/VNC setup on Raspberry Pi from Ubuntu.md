---
title: VNC setup on Raspberry Pi from Ubuntu
tags:
  - System
  - 树莓派
  - VNC
  - code
date: 2017-06-05 18:06:31
---

http://mitchtech.net/vnc-setup-on-raspberry-pi-from-ubuntu/

通过SSH连接：

```ssh pi@192.168.0.112```

默认用户名pi, 默认密码‘raspberry’

配置VNC：
```
sudo apt-get install tightvncserver
启动VNC：vncserver :1 -geometry 1024x600 -depth 16 -pixelformat rgb565
连接VNC：vncviewer 192.168.0.112:5901
停止VNC：vncserver -kill :1
```
