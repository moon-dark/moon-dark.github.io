---
title: Android 刷机常用命令
tags:
  - Android
  - 刷机
  - 命令
  - code
date: 2017-04-29 20:34:06
---

Android 刷机常用命令
* * *
### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#刷机指导-android)[刷机指导-Android](http://forum.xda-developers.com/wiki/Flashing_Guide_-_Android "Flashing Guide - Android")

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#rom-flashing)ROM Flashing

*   １、下载合适的ROM
*   ２、拷贝zip格式的ROM文件，到SD卡
*   ３、重启并进入recovery模式
*   ４、备份手机，清除数据
*   ５、菜单选择刷机
*   ６、选择ROM文件
*   ７、确定
*   ８、成功后重启
<!--more-->
##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#ruu-flashing)RUU Flashing

*   １、Android设备连接电脑。
*   ２、下载ROM升级工具（ROM Upgrade Utilities (RUUs)）
*   ３、手机通过USB连接电脑，运行RUU
*   ４、按照RUU说明刷机
*   ５、正在刷机，除非出错

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#android-flashing-guide-with-fastboot)Android Flashing Guide (with fastboot)

###### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#prerequisites前提)Prerequisites（前提）

*   需要未锁定的bootloader或开发模式的bootloader

###### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#unlocking-your-bootloader解锁bootloader)Unlocking your bootloader（解锁bootloader）

*   Nexus One (cannot be relocked).

    *   fastboot oem unlock
    *   接受／拒绝屏幕警告

*   Nexus S (can be unlocked and relocked)

    *   fastboot oem unlock
    *   接受／拒绝屏幕警告
    *   fastboot oem lock　　//重新锁定bootloader

###### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#rom-flashing-1)ROM Flashing

*   1、下载ROM，记录ROM在PC上的路径
*   2、重启手机进入fastboot mode，通过下面两种方法之一

    *   1、adb reboot bootloader (需要USB调试开关打开).
    *   2、关机长按音量加和电源按钮

*   3、清除手机数据

    *   fastboot -w

*   4、升级到ROM

    *   fastboot update &lt;/path/to/your/RomFile.zip&gt;

*   5、你的手机将刷机并自动重启到新的ROM

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#rom-update-without-sdcard)ROM update without SDcard

如果没有SD卡或者SD卡的卡槽坏了，需要第三方recovery如TWRP

*   1、进入手机Recovery模式
*   2、adb shell
*   3、mount data
*   4、Ctrl+C
*   5、adb push &lt;/path/to/your/RomFile.zip&gt; /data/
*   6、等待ROM包上传完成，使用命令adb reboot-bootloader重新进入recovery
*   7、mount data
*   8、mount /data /sdcard （挂载data文件夹到sdcard挂载点）
*   9、定位ROM的zip文件
*   10、确认刷机
*   11、刷机无错误，重启系统
*   12、删除data下的ROM刷机包（在ROOT权限下，使用文件浏览器删除，或者命令adb shell rm &lt;/path/to/your/RomFile.zip&gt;删除）

### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#安装第三方recovery)[安装第三方Recovery](http://www.addictivetips.com/mobile/how-to-install-a-custom-recovery-to-an-android-phone-device/ "How To Install A Custom Recovery To An Android Phone / Device")

主要有三种标准方法安装第三方Recovery

*   使用ROM Manager or GooManager
*   使用Fastboot
*   使用flash_image

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#install-clockworkmod-recovery-with-rom-manager)Install ClockworkMod Recovery with ROM Manager:

略...

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#install-twrp-recovery-with-goomanager)Install TWRP Recovery with GooManager:

略...

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#install-any-custom-recovery-with-fastboot)Install any Custom Recovery with Fastboot:

*   1、下载recovery镜像
Note：镜像文件为.img后缀文件，下载的如果是zip文件需要先解压缩
*   2、USB连接Android设备
*   3、进入fastboot模式

    *   adb reboot bootloader

*   4、flash

    *   fastboot flash recovery &lt;/path/to/your/recovery.img&gt;

*   5、等待进度完成，重启手机

    *   fastboot reboot

##### [](https://github.com/moon-dark/Android-Developer-Common-Commands/blob/master/%E5%88%B7%E6%9C%BA.md#install-any-custom-recovery-with-flash_image)Install any Custom Recovery with flash_image:

*   1、[下载flash image](http://cloud.addictivetips.com/wp-content/uploads/2011/01/flash_image.zip "Download flash_image")，解压到C盘根目录
*   2、下载recovery镜像
Note：镜像文件为.img后缀文件，下载的如果是zip文件需要先解压缩
*   3、开启手机开发者选项：Menu > Settings > Applications > Development
*   4、USB连接Android设备
*   5、命令行敲命令：
```
    adb push c:\flash_image /sdcard/
    adb push c:recovery.img /sdcard/
    adb shell
    su
    mount -o remount, rw /system
    cp /sdcard/flash_image /system/bin
    cd /system/bin
    chmod 777 flash_image
    flash_image recovery /sdcard/recovery.img
```

*   6、等待进度完成，重启手机
