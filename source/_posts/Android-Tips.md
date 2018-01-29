---
title: Android Tips
date: 2018-01-16 13:51:49
tags:
  - code
  - android
---
## Android使用源码库Hide函数进行译：
- 1、找到那些被隐藏起来的类、它们在以下两个位置：
A、out\target\common\obj\JAVA_LIBRARIES\framework_intermediates/classes.jar ；
B、out\target\common\obj\JAVA_LIBRARIES\android-common_intermediates\classes.jar ；
将找到的.jar文件，在Eclipse中作为User Library 添加到 Project 的Librarys依赖中，并将该库的顺序置于Android SDK Library之上。
在 out\target\common\obj\JAVA_LIBRARIES\ 目录下，还有很多其它具体应用相关的jar包，.... ；

- 2、找到那些缺失的、编译时才生成的，.java源文件，它们通常在以下位置：
A、 out\target\common\obj\JAVA_LIBRARIES\XXX_intermediates\src 目录下，XXX是相关模块的名称；
B、out\target\common\obj\APPS\XXX_intermediates\src 目录下；
将找到的src目录，在Eclipse下，以Link Source的方式，连接到Project 中作为源码的一部分。

## 事件处理机制
- 一个点击事件产生后，传递顺序是：Activity（Window） -> ViewGroup -> View
- dispatchTouchEvent() 、onInterceptTouchEvent()和onTouchEvent()
- requestDisallowInterceptTouchEvent : 参数true阻止View截获touch事件

<!-- more -->

### 获取Window宽高
```
WindowManager wm = (WindowManager) mContext.getSystemService(Context.WINDOW_SERVICE);
Display display = wm.getDefaultDisplay();
Point size = new Point();
display.getSize(size);
```

### Recyclerview展开不滑动
```
NestedScrollView and setNestedScrollingEnabled(false)
```
或
```
RecyclerView recycler = (RecyclerView) findViewById(R.id.recycler);
recycler.setNestedScrollingEnabled(false);
```

## 字符串连接：(数据绑定)
concate it with **grave accent (`)**
```
android:text="@{`Hello ` + user.firstName}"/>
```

## OQL
```
select * from instanceof android.support.v4.app.Fragment
select * from instanceof android.os.Handler
select * from instanceof java.lang.Thread
select * from instanceof  android.view.View
```

## find current activity name in activity stack.
```
adb shell dumpsys activity

# I prefer parsing results of dumpsys window over dumpsys activity
adb shell dumpsys window windows | grep -E 'mCurrentFocus|mFocusedApp'
```

## [灰度图片](http://www.curious-creature.com/2009/05/02/drawable-mutations/)
```
ColorMatrix matrix = new ColorMatrix();
matrix.setSaturation( 0);
filter = new ColorMatrixColorFilter(matrix) ;

img.getDrawable().mutate().setColorFilter(isFull ? filter : null );
```

## 获得图片(eg:圆角图片)
```
Bitmap mRes = BitmapFactory.decodeResource(getResources(), R.drawable.experience_card) ;
mRes = BitmapIncise.getRoundedCornerBitmap (mRes, radiusCallback.getRoundPx());
img.setImageBitmap(mRes);
```

```
//获得圆角图片的方法
public static Bitmap getRoundedCornerBitmap(Bitmap bitmap , float roundPx) {
     Bitmap output = Bitmap. createBitmap(bitmap.getWidth() , bitmap
                                   .getHeight() , Config.ARGB_8888) ;
     Canvas canvas = new Canvas(output);

     final int color = 0xff424242 ;
     final Paint paint = new Paint();
     final Rect rect = new Rect(0, 0, bitmap.getWidth() , bitmap.getHeight());
     final RectF rectF = new RectF(rect);

     paint.setAntiAlias(true );
     canvas.drawARGB(0 , 0, 0, 0 );
     paint.setColor(color);
     canvas.drawRoundRect(rectF, roundPx , roundPx, paint);

     paint.setXfermode(new PorterDuffXfermode(Mode. SRC_IN));
     canvas.drawBitmap(bitmap, rect , rect, paint);

     return output;
}
```

## Android日志
```
Log. i("==>", Log.getStackTraceString(new Exception())) ;
// or
new Exception().printStackTrace();
```

## [fix控件GridView滚动到底部](https://stackoverflow.com/questions/25357568/how-to-preven-the-gridview-scroll-by-default-once-dataset-changed)
Issue:
```
GridView is being scrolled automatically.
```
Reason:
```
When the screen is loaded, it finds the first focusable View from its ViewHierarchy and sets the focus to that View.
```
Solution:
```
You can set focus to some other View so that Android does not focus the GridView at first,so it won't scroll automatically.
```
Example:
```
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/root">

<RelativeLayout
        android:padding="10dp"
        android:id="@+id/rlContainer"           //you may set id and requestfocus from java code as well
        android:focusable="true"                //add this
        android:focusableInTouchMode="true"     //add this
        android:layout_width="match_parent"
        android:layout_height="match_parent">
    .........                                     //you can also add focusable,focusableInTouchMode to any other view before the GridView
    <com.test.android.view.ScrollableGridView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:numColumns="auto_fit"
            android:columnWidth="100dp"
            android:verticalSpacing="2dp"
            android:focusable="false"
            android:clickable="false">
    </com.test.android.view.ScrollableGridView>
</RelativeLayout>
```
From java code,

inside onCreate() method of your Activity
```
RelativeLayout rlContainer = (RelativeLayout) findViewById(R.id.rlContainer);
rlContainer.requestFocus();
```

## [Run java on Android](https://stackoverflow.com/questions/10199863/how-to-execute-the-dex-file-in-android-with-command)
Let's say you have a the following code in file HelloWorld.java:
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

To run it on an android device:
```
javac HelloWorld.java
dx --dex --output=classes.dex HelloWorld.class
zip HelloWorld.zip classes.dex
adb push HelloWorld.zip /sdcard/
```

For GB or earlier, you should be able to simply do:
```
adb shell dalvikvm -cp /sdcard/HelloWorld.zip HelloWorld
```

For ICS+:
```
adb shell mkdir /sdcard/dalvik-cache
adb shell ANDROID_DATA=/sdcard dalvikvm -cp /sdcard/HelloWorld.zip HelloWorld
```

磁盘状态：
```java
import android.os.StatFs;

public class FreeDisk {
    public static void main(String[] args) {
        System.out.println("\nFreeMemory = " + FreeMemory("/sdcard/"));
    }

    public static int FreeMemory(String path) {
        StatFs statFs = new StatFs(path);
        int Free = (statFs.getAvailableBlocks() * statFs.getBlockSize()) / 1048576;
        long LongFree = ((long)statFs.getAvailableBlocks() * (long)statFs.getBlockSize()) / 1048576l;
        double DoubleFree = ((double)statFs.getAvailableBlocks() * (double)statFs.getBlockSize()) / 1048576;

        System.out.println("statFs.getAvailableBlocks() = " + statFs.getAvailableBlocks());
        System.out.println("statFs.getBlockSize() = " + statFs.getBlockSize());
        System.out.println("Free = " + Free);
        System.out.println("LongFree = " + LongFree);
        System.out.println("DoubleFree = " + DoubleFree);

        return Free;
    }
}
```

## [Android指向Resource的URI](http://www.blogjava.net/anchor110/articles/338601.html)
A Uri object can be used to reference a resource in an APK file. The Uri should be one of the following formats:

- **android.resource://package_name/id_number**
  ``package_name`` is your package name as listed in your AndroidManifest.xml. For example com.example.myapp
  ``id_number`` is the int form of the ID.
  The easiest way to construct this form is
``Uri uri = Uri.parse("android.resource://com.example.myapp/" + R.raw.my_resource");``

- **android.resource://package_name/type/name**
  ``package_name`` is your package name as listed in your AndroidManifest.xml. For example com.example.myapp
  ``type`` is the string form of the resource type. For example, raw or drawable.
  ``name`` is the string form of the resource name.
  That is, whatever the file name was in your res directory, without the type extension.
   The easiest way to construct this form is
``Uri uri = Uri.parse("android.resource://com.example.myapp/raw/my_resource");``

## [Java虚拟机类型签名](http://trylovecatch.iteye.com/blog/1292570)
表 3-2 Java虚拟机类型签名

类型签名 | Java 类型
---|---
Z | boolean
B | byte
C | char
S | short
I | int
J | long 注意 这个是J
F | float
D | double
L | fully-qualified-class ; 全限定的类
[type | type[]
(arg-types)ret-type | 方法类型

 例如，Java 方法：
     ``long fun(int n, String s, int[] arr);`` <br>
 具有以下类型签名：
     ``(ILjava/lang/String;[I)J``
