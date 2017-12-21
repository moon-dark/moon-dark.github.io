---
title: Linux服务：阿里云/树莓派配置BT下载软件Transmission
date: 2017-12-21 09:10:55
tags:
  - code
  - server
---

因为树莓派在长城宽带多层NAT下面，BT下载软件全红。所以想阿里云上配置BT服务：

### 先按照树莓派的方案安装Transmission软件：

安装transmission-daemon
``` Bash
sudo apt-get install transmission-daemon
```

安装完成后第一件事情就是停止transmission-daemon服务，否则你后面做的修改保存不下来的。
```
sudo service transmission-daemon stop
```

配置transmission-daemon
```
sudo vim /etc/transmission-daemon/settings.json
```

一般使用，只需要修改一下下面的内容就好了，需要查看完整的参数说明，请[参考官网](https://trac.transmissionbt.com/wiki/ConfigurationParameters)
```
# 下面这个参数指定下载地址
"download-dir": "/home/pi/media/bt/downloads",
# 下面这个参数指定同时下载的任务数量
"download-queue-size": 2,
# 下面这个参数表示未完成的任务放在另一个地方，我们就不用了。
"incomplete-dir-enabled": false,
# 下面的参数开启远程web管理界面
"rpc-enabled": true,
# 下面这个参数表示web管理界面是否需要密码，我们选是
"rpc-authentication-required": true,
# 下面的参数就是web管理界面的管理信息，大家看着改成自己的内容就好了
"rpc-username": "登录名",
"rpc-password": "明文密码（保存后自动变成密文）",
"rpc-port": 9091,
```

<!-- more -->

需要改的就这么多，其他的使用默认的就好了。为了能避免出现读写权限问题，我们直接把你要下载的下载目录改成777权限，如下：
```
sudo chmod 777 -R bt
```

重启服务
```
sudo service transmission-daemon reload
sudo service transmission-daemon start
```

没有错误的话，打开浏览器，在地址栏输入 树莓派的ip:9091 看一下是不是成功启动了？以后直接上传自己的种子文件，他就会自动下载了。

### 配置Aliyun防火墙：
使用netstat命令查看9091端口已经正常开启了。可是仍然连不上web客户端.

查看内核版本
```
#uname -a
Linux AY140607183050278fb7Z 3.2.0-29-generic #46-Ubuntu SMP Fri Jul 27 17:03:23 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux
```
查看发行版本
```
#cat /proc/version
Linux version 3.2.0-29-generic (buildd@allspice) (gcc version 4.6.3 (Ubuntu/Linaro 4.6.3-1ubuntu5) ) #46-Ubuntu SMP Fri Jul 27 17:03:23 UTC 2012
```
根据显示的结果，ECS服务器采用Ubuntu 2012年的Linux发行版本，Linux的内核版本为3.2.0。

检查系统默认防火墙（Ubuntu自带默认的防火墙为：UFW）是否开启，利用以下命令：
```
#sudo ufw status
Status: inactive
```
发现系统的防火墙处于关闭状态，那么就无须考虑防火墙对端口的控制了。

linux查看iptables防火墙状态及开启关闭命令，对Ali云失效
```
存在以下两种方式：
一、service方式
查看防火墙状态：
[root@centos6 ~]# service iptables status
iptables：未运行防火墙。
开启防火墙：
[root@centos6 ~]# service iptables start
关闭防火墙：
[root@centos6 ~]# service iptables stop

二、iptables方式
先进入init.d目录，命令如下：
[root@centos6 ~]# cd /etc/init.d/
[root@centos6 init.d]#
然后
查看防火墙状态：
[root@centos6 init.d]# /etc/init.d/iptables status
暂时关闭防火墙：
[root@centos6 init.d]# /etc/init.d/iptables stop
重启iptables：
[root@centos6 init.d]# /etc/init.d/iptables restart
```

iptables默认是开启的：
```
/sbin# ll iptables*
lrwxrwxrwx 1 root root   13 Jan  9  2014 iptables -> xtables-multi*
-rwxr-xr-x 1 root root 7016 Jan  9  2014 iptables-apply*
lrwxrwxrwx 1 root root   13 Jan  9  2014 iptables-restore -> xtables-multi*
lrwxrwxrwx 1 root root   13 Jan  9  2014 iptables-save -> xtables-multi*
```

利用以下命令开放端口9091：
```
/sbin# iptables -A INPUT -p tcp --dport 9091 -j ACCEPT
```
保存修改：
```
/sbin# iptables-save
```

对于阿里云ECS需要通过控制台设置来实现公网端口的开放

| 授权策略 |	协议类型	| 端口范围	| 授权类型	| 授权对象	| 描述	| 优先级	| 创建时间 |
| --- |  --- |  --- |  --- |  --- |  --- |  --- |  --- |
| 允许	| 自定义 TCP	|9091/9091	| 地址段访问	| 0.0.0.0/0 | Transmission | 1 |	2017-12-21 10:00:36 |

下载速度很快，听说传输到本地很慢
![](img/BT20171221.png)
