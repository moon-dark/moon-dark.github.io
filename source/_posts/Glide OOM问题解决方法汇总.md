---
title: Glide OOM问题解决方法汇总
tags:
  - Android
  - code
date: 2018-04-16 16:13:00
---

1、引入largeHeap属性，让系统为App分配更多的独立内存。
2、禁止Glide内存缓存。设置skipMemoryCache(true)。
3、自定义GlideModule。设置MemoryCache和BitmapPool大小。
4、升级到Glide4.0，使用asDrawable代替asBitmap，drawable更省内存。
5、ImageView的scaleType为fitXY时，改为fitCenter/centerCrop/fitStart/fitEnd显示。
6、不使用application作为context。当context为application时，会把imageView是生命周期延长到整个运行过程中，imageView不能被回收，从而造成OOM异常。
7、使用application作为context。但是对ImageView使用弱引用或软引用，尽量使用SoftReference，当内存不足时，将及时回收无用的ImageView。
8、当列表在滑动的时候，调用Glide的pauseRequests()取消请求，滑动停止时，调用resumeRequests()恢复请求。
9、Try catch某些大内存分配的操作。考虑在catch里面尝试一次降级的内存分配操作。例如decode bitmap的时候，catch到OOM，可以尝试把采样比例再增加一倍之后，再次尝试decode。
10、BitmapFactory.Options和BitmapFactory.decodeStream获取原始图片的宽、高，绕过Java层加载Bitmap，再调用Glide的override(width,height)控制显示。
11、图片局部加载。参考：SubsamplingScaleImageView，先将图片下载到本地，然后去加载，只加载当前可视区域，在手指拖动的时候再去加载另外的区域。
