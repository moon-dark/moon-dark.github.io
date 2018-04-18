---
title: Linux 查看登录日志及登录失败用户的ip && SSH限制ip登陆
date: 2017-11-12 11:30:11
tags:
  - code
  - Shell
  - ssh
---
### Linux 查看登录成功的用户信息
命令: **last**

### 最新的登录记录在最前面，所以可以用 一下命令来查看。

命令: **last | less**

### 查看登录失败的用户信息

命令: **lastb**

### 查看登录日志

命令:  **tail /var/log/secure**

<!-- more -->

### SSH限制ip登陆
在/etc/hosts.allow输入   
  (其中192.168.10.88是你要允许登陆ssh的ip,或者是一个网段192.168.10.0/24)   
  ```
  sshd:192.168.10.88:allow
  ```

在/etc/hosts.deny输入(表示除了上面允许的，其他的ip   都拒绝登陆ssh)   
  ```
  sshd:ALL
  ```

更改/增加端口
  ```
  vi /etc/ssh/sshd_config
  port 3333

  #最后一行加上ip
  allowusers root@ip   #允许某个ip用什么帐户登陆
  ```
