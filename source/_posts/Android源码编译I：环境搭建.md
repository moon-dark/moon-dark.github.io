---
title: Android源码编译I：环境搭建
tags:
  - Android
  - 源码
  - code
date: 2017-02-16 10:35:54
---

一、目标：

完成Android源码编译

二、环境：

桌面环境：Win7系统，Git bash，Chrome浏览器（插件：代理助手），ShadowSocks

<!--more-->

三、步骤：

**1、配置虚拟机：**

1）安装：从http://www.virtualbox.org/wiki/Downloads下载最新VirtualBox并安装。

2）安装64位Ubuntu 16：名称AndroidSource

3）设置共享文件夹：

VirtualBox：

控制&gt;设置&gt;共享文件夹&gt;添加共享文件夹：选择一个目录，勾选固定分配。（如D:\share名称share）

设备&gt;安装增强功能：安装结束重启

4）设置NAT：

控制&gt;设置&gt;网络&gt;网络地址转换NAT的高级&gt;端口转发：增加SSH转发，TCP协议，主机127.0.0.1，22端口，转发到子系统ip，22端口。

5）配置SSH服务：Terminal下执行

```sudo apt-get install openssh-server
service ssh restart```

**2、配置源码环境：**参考http://source.android.com/source/initializing.html

Git bash登录Ubuntu：

```ssh 用户名@localhost```

1）安装JDK：

```$ sudo apt-get update
$ sudo apt-get install openjdk-8-jdk```

2）安装编译依赖包：

```$ sudo apt-get install git-core gnupg flex bison gperf build-essential \
zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 \
lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache \
libgl1-mesa-dev libxml2-utils xsltproc unzip```

3）配置Host文件：

```vim /etc/hosts```

，增加以下内容：

```
173.194.72.82 www.googlesource.com
173.194.72.82 android.googlesource.com
37.61.54.158 cache.pack.google.com
173.194.74.82 gerrit.googlesource.com
```

4）下载repo工具：

```
$ mkdir ~/bin
$ PATH=~/bin:$PATH
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

**3、下载Android源码：**
参考https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/

```
sudo chown hamilton:hamilton /opt/ #hamilton:hamilton为用户名:用户组
mkdir /opt/AOSP/
cd /opt/AOSP/

$ git config --global user.name "Your Name"
$ git config --global user.email "you@example.com"

repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest
```

git目录位于.repo/manifests，可以这样查看Android源码分支：

```
cd .repo/manifests
git branch -a
cd ../../
```

同步Android Nougat：

```
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-7.1.1_r22
repo sync
```

四、漫长的等待：
