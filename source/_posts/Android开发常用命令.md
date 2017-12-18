---
title: Android开发常用命令
date: 2017-12-18 20:24:54
tags:
  - Android
  - 命令
  - code
---

Android开发常用命令

* * *

### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#adb-shell-命令)[ADB Shell 命令](https://developer.android.com/studio/command-line/shell.html "ADB Shell 命令")
```Bash
adb shell screencap /sdcard/screen.png  #截图
adb shell screenrecord /sdcard/demo.mp4  #录像，Ctrl+C停止
```
<!--more-->
### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#adb-debug-命令)[ADB Debug 命令](https://developer.android.com/studio/command-line/adb.html "ADB Debug 命令")
```Bash
adb devices  #查看adb连接设备列表
adb -s serialNumber command #在设备serialNumber上执行command

adb install path_to_apk #安装Apk
adb forward tcp:6100 tcp:7100 #端口转发，主机TCP端口6100转发到设备TCP端口7100
adb pull remote local #拷贝（拉取）设备上的文件
adb push local remote #拷贝（推送）文件到设备上

adb kill-server     #终止adb服务
adb start-server    #启动adb服务

#Wireless usage
adb tcpip 5555   #设备adb服务监听5555端口
adb connect  device-ip-address #连接设备TCP端口上的adb服务
```

### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#monkey命令)[monkey命令](https://developer.android.com/studio/test/monkey.html "monkey命令")

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#android之monkey全参数包含隐藏参数-)[Android之Monkey全参数（包含隐藏参数）](http://blog.csdn.net/jlminghui/article/details/38238443 "Android之Monkey全参数（包含隐藏参数） ")
```Bash
adb shell monkey [options] event-count #eg : adb shell monkey -s 12345 -v 10000 -p com.example
```
### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#tcpdump命令)[tcpdump命令](http://mysuperbaby.iteye.com/blog/902201 "tcpdump命令")

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#linux-tcpdump命令详解)[Linux tcpdump命令详解](http://www.cnblogs.com/ggjucheng/archive/2012/01/14/2322659.html "Linux tcpdump命令详解")

需要ROOT，下载到Windows/Mac后使用Wireshark打开
```Bash
adb shell tcpdump -s 0  -w /sdcard/a.pcap  "tcp port 80"   #捕获80端口的tcp数据包，保存到/sdcard/a.pcap
```
### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#android命令)[Android命令](http://stackoverflow.com/questions/17963508/how-to-install-android-sdk-build-tools-on-the-command-line "Android命令")
`How to install Android SDK Build Tools on the command line?
By default, the SDK Manager from the command line does not include the build tools in the list. They're in the "obsolete" category. To see all available downloads available, use
`android list sdk --all`
And then to get one of the packages in that list from the command line, use:
`android update sdk -u -a -t &lt;package no.&gt;`
Where -u stands for --no-ui, -a stands for --all and -t stands for --filter.
If you need to install multiple packages do:
`android update sdk -u -a -t 1,2,3,4,..,n `
Where 1,2,..,n is the package number listed with the list command above
`

### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%BC%80%E5%8F%91.md#android代理)[Android代理](http://stackoverflow.com/questions/10634202/android-sdk-manager-proxy-settings-in-linux "Android代理")

The solution was to setup a local proxy to perform authentication and create the file ~/.android/androidtool.cfg to redirect android to the local proxy as follows.
```
### Settings for Android Tool
#Tue Jun 12 01:34:55 PDT 2012
http.proxyPort=3128
sdkman.monitor.density=108
http.proxyHost=127.0.0.1
sdkman.show.update.only=true
sdkman.ask.adb.restart=false
sdkman.force.http=true
sdkman.show.updateonly=true
```

### AOSP下载代码
```Bash
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```
```shell
#AOSP download command
#https://source.android.com/source/downloading
repo init -u https://android.googlesource.com/platform/manifest
repo init -u https://android.googlesource.com/platform/manifest -b android-7.1.2_r6

#lineageos download command
#https://wiki.lineageos.org/devices/bacon/build#download-the-source-code
cd ~/android/lineage
repo init -u https://github.com/LineageOS/android.git -b cm-14.1
```

```Bash
repo sync -c -d -j2 --no-tags
```


## 危险操作
### 更新framework:
```
adb root
adb remount

adb push services.jar /system/framework
adb push framework.jar /system/framework
adb push framework-res.apk /system/framework

adb push arm/boot.oat /system/framework/arm
adb push arm/boot.art /system/framework/arm
adb push arm/boot-framework.oat /system/framework/arm
adb push arm/boot-framework.art /system/framework/arm

adb push arm64/boot.oat /system/framework/arm64
adb push arm64/boot.art /system/framework/arm64
adb push arm64/boot-framework.oat /system/framework/arm64
adb push arm64/boot-framework.art /system/framework/arm64

pause
adb reboot
```
### 更新SystemUI
```
echo off
echo YES | copy \\ip\&lt;branch1&gt;\out\target\product\&lt;product&gt;\system\priv-app\SystemUI\SystemUI.apk .
md5sum SystemUI.apk
adb push SystemUI.apk /system/priv-app/SystemUI/SystemUI.apk
adb shell am force-stop com.android.systemui
echo on
```

### 编译framework:
```
mmm core/res && mm && mmm services && mm
mmm -B core/res && mm -B && mmm -B services && mm -B
```
