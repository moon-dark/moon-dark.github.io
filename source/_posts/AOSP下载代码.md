---
title: AOSP下载代码
tags:
  - Android
  - 源码
  - code
date: 2017-05-01 12:30:03
---

```Bash
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```
<!--more-->
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
