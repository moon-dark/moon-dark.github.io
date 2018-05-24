---
title: Android 阅读 Office 文档
date: 2018-02-08 21:35:04
tags:
 - Android
 - code
 - Google
---

# 可用的解决方案：
Android 阅读 Office 文档，总体来说，一是采用开源库，本地解析；二是采用WebView 网页显示，三是打开本地应用。参考[Android 阅读 Office 文档，所有你能想到的解决方案都在这啦](http://yifeng.studio/2016/11/06/android-office-document-viewer/)所说的，主流的有如下几种：

### WebView 网页显示：
Google Doc
---
类似 iOS ，Google 也提供了一种在线文档解析的功能，只需要按照固定的格式将远程文档的 Url 地址传给 Google 服务器，即可利用 WebView 控件加载新的 Url 地址，显示即可。WebView 加载的 Url 地址格式如下：
```
https://docs.google.com/gview?embedded=true&url=文档地址
```
优点：使用简单，无需服务器和客户端的额外部署工作。
缺点：国内需要翻墙访问。

### 打开本地应用
如果移动设备中安装有能够打开 Office 文档的本地应用，也可以借助它们打开所要操作的文档，当然前提是必须先将远程文档下载到设备的本地存储空间中：
```
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
//officeFile：本地文档；type：文档MIMEType类型，可以使用文件格式后缀
intent.setDataAndType(Uri.fromFile(officeFile), type);
if (intent.resolveActivity(getPackageManager())!=null){
  startActivity(intent);
}
```
优点：可读可写。
缺点：需要先行下载，同时设备中存在能够打开文档的本地应用，并且阅读时会离开当前应用。
<!-- more -->

### 第三方 SDK
#### 免费开源
- [pdfium](https://android.googlesource.com/platform/external/pdfium/) Google 的开源项目，也是 Chrome 浏览器的PDF渲染引擎，初始代码来自国内知名PDF技术公司「福昕」。
- [AndroidPdfViewer](https://github.com/barteksc/AndroidPdfViewer)
- [PdfiumAndroid](https://github.com/barteksc/PdfiumAndroid)  基于 pdfium 的两个 Android 平台实现方式，支持 PDF 文档的应用内预览，支持动画、缩放、手势和双击操作。
- [MuPDF](http://mupdf.com/)  一个轻量级的 开源 PDF 和 XPS 查看器。
- [腾讯浏览服务（TBS，Tencent Browsing Service）](https://www.jianshu.com/p/3f57d640b24d)
#### 付费 SDK
- [Foxit PDF SDK](http://www.foxitsoftware.cn/products/sdk/PDFsdk/android/) 福昕出品，性能稳定，功能强大，支持 PDF 的显示、导航、创建、搜索、注释、保护、PDF文本提取、图片转换、表单数据收集和编辑等功能。
- [plugPDF](https://plugpdf.com/) 来自国外的一个付费 SDK，使用简单，只需三步即可集成到自己的应用中并使用。

### 服务器端解析
- [PDF.js](http://mozilla.github.io/pdf.js/) 「 mozilla 」开源的一种JS解决方案，提供了服务器和客户端两种集成方式。
- [pdf2htmlEX](http://coolwanglu.github.io/pdf2htmlEX/) 类似 ``PDF.js``，在服务器端集成，利用 Web技术完成 Office 文档的解析工作。


# 采用的解决方案：Google Doc
参考[Nginx Module for Google](https://github.com/cuber/ngx_http_google_filter_module)：
[Nginx Google 扩展,让google反向代理的配置和使用wen.lu一样简单.](http://www.dnsdizhi.com/post-262.html):

### 配置Google代理

#### [Nginx 多规则替换模块 ngx_http_substitutions_filter_module](http://www.liuzhixiang.com/2014/12/19/Nginx-ngx_http_substitutions_filter_module/)
Nginx作为反向代理时有一个ngx_http_sub_module模块可以修改响应的内容: http://nginx.org/en/docs/http/ngx_http_sub_module.html，但缺点是只能使用一条规则。而ngx_http_substitutions_filter_module这个第三方的模块可以做到多条规则。

##### 安装
下载ngx_http_substitutions_filter_module
需要使用git下载安装包，可以使用sudo yum install git安装或直接到https://github.com/yaoweibin/ngx_http_substitutions_filter_module下载最新的源码包。
```
git clone git://github.com/yaoweibin/ngx_http_substitutions_filter_module.git
```
安装Nginx
安装需要依赖[pcre(PERL 5 regular expression pattern matching): ](http://sourceforge.net/projects/pcre/files/pcre/)和[zlib: ](http://sourceforge.net/projects/libpng/files/zlib/)，这里事先下载好并解压。
```
wget http://nginx.org/download/nginx-1.7.8.tar.gz
tar zxvf nginx-1.7.8.tar.gz
cd nginx-1.7.8
./configure --with-pcre=../pcre-8.36 --with-zlib=../zlib-1.2.8 --add-module=../ngx_http_substitutions_filter_module/
make
sudo make install
```
上面configure命令中 ``../pcre-8.36`` ``../zlib-1.2.8`` ``../ngx_http_substitutions_filter_module/``分别是``pcre``、``zlib``与``ngx_http_substitutions_filter_module``的源码路径。

Nginx被默认安装在/usr/local/nginx/目录。

##### 使用
模块只有两条指令：

**subs_filter_types**

语法: subs_filter_types mime-type [mime-types]

默认: subs_filter_types text/html

适用范围: http, server, location

subs_filter_types指令指定需要替换内容的MIME类型，默认是text/html类型。

需要注意的是：如果服务器会对内容进行了gzip压缩，则要加入以下指令使服务器返回未经压缩的内容才能起作用：
```
proxy_set_header Accept-Encoding "";
```

**subs_filter**

语法: subs_filter 源字符串 替换为的字符串 [gior]

默认: none

使用范围: http, server, location

subs_filter使用直接替换或者正则匹配替换的方式替换。

第三个参数的含义是：
- g(默认): 替换所有匹配的字符串
- i：区分大小写
- o：仅替换首个匹配的字符串
- r：使用正则匹配

例子
```
location / {

    proxy_pass http://example.com/; #反向代理
    proxy_set_header Accept-Encoding ""; #防止后端服务器在返回gzip后的内容时模块不起作用
    subs_filter_types text/html text/css text/xml; #替换html、css、xml内容
    subs_filter st(\d*).example.com $1.example.com ir; #使用正则替换
    subs_filter a.example.com s.example.com; #使用直接匹配替换

}
```
相似的模块
Nginx Module for Google: https://github.com/cuber/ngx_http_google_filter_module

#### 编译安装：
下载nginx源码，并编译：
```
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module  \
    --with-pcre=../nginx_ext/pcre-8.41    \
    --with-openssl=../nginx_ext/openssl    \
    --with-zlib=../nginx_ext/zlib-1.2.11    \
    --add-module=../nginx_ext/ngx_http_google_filter_module    \
    --add-module=../nginx_ext/ngx_http_substitutions_filter_module

make
```

编译中标准库出错：
```
objs/addon/src/ngx_http_google_util.o \
objs/addon/ngx_http_substitutions_filter_module/ngx_http_subs_filter_module.o \
objs/ngx_modules.o \
-ldl -lpthread -lcrypt ../nginx_ext/pcre-8.41/.libs/libpcre.a ../nginx_ext/openssl/.openssl/lib/libssl.a ../nginx_ext/openssl/.openssl/lib/libcrypto.a -ldl ../nginx_ext/zlib-1.2.11/libz.a \
-Wl,-E
../nginx_ext/openssl/.openssl/lib/libcrypto.a(threads_pthread.o): In function `fork_once_func':
threads_pthread.c:(.text+0xad): undefined reference to `pthread_atfork'
collect2: ld returned 1 exit status
make[1]: *** [objs/nginx] Error 1
make[1]: Leaving directory `/root/nginx-1.11.8'
make: *** [build] Error 2

```

修改Makefile文件：
```
# vim objs/Makefile
```
将``-ldl -lpthread -lcrypt``改为``-ldl -pthread -lcrypt``

编译并安装：
```
make
make install
```

配置nginx.conf
```
# vi /usr/local/nginx/conf/nginx.conf

server {
    listen       443 ssl;
    server_name  <保密数据>;
    resolver 8.8.8.8;

    ssl_certificate /etc/letsencrypt/live/<????>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<????>/privkey.pem;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers  on;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }

    location / {
        root   /var/www/html;
        index  index.html index.htm;
        google on;
        google_docs on;
        google_scholar on;
        add_header Content-Security-Policy upgrade-insecure-requests;
        add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
    }
}

```

### 使用Docker快速布置
下载docker镜像：
```
docker pull humilton/nginx-google-proxy
```

下载源码：
```
$ git clone https://github.com/humilton/docker-nginx-google-proxy.git
$ cd docker-nginx-google-proxy
```

编辑 nginx.conf，配置自己的域名：
```
# vi nginx.conf
use command :s/web.yaoping.win/<your domain replace google.com>/g
use command :s/w.yaoping.win/<your domain replace docs.google.com>/g
use command :s/api.yaoping.win/<your domain replace apis.google.com>/g
```

使用docker-compose挂载nginx.conf, 执行docker:
```
$ docker-compose up   # run `docker-compose up -d` for run as daemon
$ docker-compose down  # shutdown docker
```

nginx服务映射到当钱服务的8081端口。
