---
title: Android中图片在内存中的大小
tags:
  - Android
  - 内存
  - code
date: 2017-02-21 11:54:52
---

Android中一张图片（Bitmap）占用的内存由以下三个因素决定：
> 1.图片的像素点个数
>
> 2.单位像素占用的字节数
图片占用的内存值为：

```图片长度  *  图片宽度  *  单位像素占用的字节数```

单位像素所占用的字节数与图片的解码方式有关。<!--more-->

查看[Bitmap.Config](http://developer.android.com/reference/android/graphics/Bitmap.Config.html)，Android支持的图片解码方式有以下4种。

### [Android所支持的图片解码方式](http://ww1.sinaimg.cn/mw1024/e3dc9ceagw1esgrnkvesjj20iv06fjtq.jpg)

其中：
ALPHA_8只存储透明度信息而无颜色值；
ARGB_8888是指把每个像素点的透明度、R、G、B色值都用一个byte来表示，即需要4字节的空间来存储一个像素点的颜色信息。
RGB_565:仅需要2个字节的空间来存储每个像素点颜色信息，是由于它不存储像素点的透明度，而且使用5比特表示R色值，6比特表示G色值，5比特表示B色值。

由上可以看出，对同一张图片进行解码时，使用RGB_565的解码方式所占用的内容空间仅仅是ARGB_8888方式的50%。
