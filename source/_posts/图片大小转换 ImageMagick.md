---
title: 图片大小转换 ImageMagick
date: 2017-04-29 20:12:51
tags:
  - Tools
  - ImageMagick
  - code
---

[图片转换 ImageMagick](http://www.charry.org/docs/linux/ImageMagick/ImageMagick.html "图片转换 ImageMagick")
```Bash
convert -flop foo.png bar.png
convert foo.jpg foo.png  #格式转化
convert -resize 100x100 foo.jpg thumbnail.jpg  #大小缩放
convert -resize 50%x50% foo.jpg thumbnail.jpg  #大小缩放
convert -mattecolor "#000000" -frame 60x60 yourname.jpg rememberyou.png  #加边框
convert -border 60x60 -bordercolor "#000000" yourname.jpg rememberyou.png #加边框
convert -fill green -pointsize 40 -draw 'text 10,50 "charry.org"' foo.png bar.png #加文字
convert -blur 80 foo.jpg foo.png  #高斯模糊
convert -flip foo.png bar.png  #上下翻转
convert -flop foo.png bar.png  #左右翻转
convert -negate foo.png bar.png  #反色,形成底片的样子
convert -monochrome foo.png bar.png #单色,把图片变为黑白颜色：
convert -noise 3 foo.png bar.png #加噪声
convert -paint 4 foo.png bar.png #油画效果
convert -rotate 30 foo.png bar.png #旋转
convert -charcoal 2 foo.png bar.png #炭笔效果
convert -spread 30 foo.png bar.png #散射,毛玻璃效果：
convert -swirl 67 foo.png bar.png  #漩涡,以图片的中心作为参照，把图片扭转，形成漩涡的效果：
convert -raise 5x5 foo.png bar.png #凸起效果
```
