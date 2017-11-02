---
title: 命令流水帐之一：配置Server环境
id: 103
categories:
  - 未分类
tags:
---

一、目标：Linux系统，Fuck GFW，HTTP服务器，MySQL

二、环境：

桌面环境：Win10系统，Git bash，Chrome浏览器（插件：代理助手），ShadowSocks

服务器环境：CentOS6.4 32位操作系统，Taobao购买国外VPS

三、步骤：

1、登录：

ssh -D 1080 root@ip  //Tunnel方式翻~墙，并登录CentOS

ssh root@ip  //正常登录

2、建立账号密码：

   passwd  //修改root密码

   adduser Hamilton  //新建帐户Hamilton
   passwd Hamilton   //设置Hamilton的密码
   usermod -G root Hamilton //增加到root组

3、设置Shadowsocks服务器：

curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"  // 获取pip
python get-pip.py //安装pip
pip install --upgrade pip //更新pip
python -V  //显示python版本号
pip install shadowsocks  //安装shadowsocks
vi /etc/shadowsocks.json  //配置shadowsocks配置文件
ssserver -c /etc/shadowsocks.json -d start  //启动shadowsocks
netstat -lnt //查看TCP监听端口

桌面环境配置与shadowsocks.json保持一致

4、设置Tomcat服务：  //J2EE环境的HTTP服务

//设置Java环境
wget "http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-i586.rpm?AuthParam=1484400707_4ae696b8bb5279d91bb9f7d374f395e2"  //从oracle.com下载JDK，下载JRE也可启动Tomcat
rpm -ivh jdk-8u111-linux-i586.rpm  //安装jdk，会安装到/usr/java/目录下面
vi /etc/profile  //增加Java环境变量
. /etc/profile   //应用Java环境变量

//配置tomcat环境
wget http://mirror.nexcess.net/apache/tomcat/tomcat-8/v8.5.9/bin/apache-tomcat-8.5.9.tar.gz
tar xvf apache-tomcat-8.5.9.tar.gz -C <path>  //解压缩tomcat
chmod -R 770 webapps/    //
cd <path>/apache-tomcat-8.5.9/bin
vi ../conf/server.xml  //配置80端口
./startup.sh           //启动服务，浏览器看效果
./shutdown.sh          //关闭服务

/etc/profile设置：
export JAVA_HOME=/usr/java/jdk1.8.0_111
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin

5、配置MySQL服务

yum list installed | grep mysql  //列出已经安装的mysql软件
yum -y remove  mysql-libs.i686   //删除
yum list | grep mysql            //列出可以安装的mysql软件
yum -y install mysql-server.i686 mysql.i686 mysql-devel.i686  //安装服务端、客户端，还有不知道什么鬼
/usr/bin/mysqladmin -u root password  //设置MySQL用户root的密码
service mysqld start             //启动mysql服务
mysql -u root -p                 //登录一下mysql

chkconfig --list | grep mysqld   //查看开机启动，过滤mysqld
chkconfig mysqld on              //设置mysqld服务开机启动

6、安装vim:

rpm -qa|grep vim  //查看已经安装的vim软件
yum list| grep vim
yum -y install vim-common.i686 vim-enhanced.i686 vim-minimal.i686 //安装

7、安装gcc, g++

yum list | grep gcc
yum install gcc-c++.i686 gcc.i686

8、配置nginx:

//文档地址：http://nginx.org/en/docs/configure.html
wget http://nginx.org/download/nginx-1.11.8.tar.gz //源码包
tar xvf nginx-1.11.8.tar.gz
cd nginx-1.11.8
yum -y install pcre-devel openssl openssl-devel   //安装缺失的环境
./configure
make                                              //默认编译到目录/usr/local/nginx下

cd /usr/local/nginx/sbin
nginx   //启动nginx
nginx -s signal  //signal使用对应的信号：stop， quit， reload， reopen
//stop — fast shutdown
//quit — graceful shutdown
//reload — reloading the configuration file
//reopen — reopening the log files
cd ..

vi conf/nginx.conf        //事先修改tomcat端口，并配置给nginx转发
./sbin/nginx              //查看效果
ln -s /usr/local/nginx/sbin/nginx /sbin/nginx //配置软链接
cd
nginx -s stop

9、配置开机启动：

vi /etc/rc.d/rc.local

增加语句：

sh <path>/apache-tomcat-8.5.9/bin/startup.sh
ssserver -c /etc/shadowsocks.json -d start
nginx

四、其它配置

配置Tomcat多域名：

在 <host name="localhost" ... ></host>下增加：

      <host name="domain1.win"  appBase="/home/user1/www"
            unpackWARs="true" autoDeploy="true">
            <alias>domain1.win</alias>
            <logger className="org.apache.catalina.logger.FileLogger" directory="logs" prefix="user1_log." suffix=".txt"  timestamp="true"/>
            <context path="" docBase="/home/user1/www" reloadable="true" caseSensitive="false" debug="0"></context>
      </host>

      <host name="domain2.win"  appBase="/home/user2/www"
            unpackWARs="true" autoDeploy="true">
            <alias>domain2.win</alias>
            <logger className="org.apache.catalina.logger.FileLogger" directory="logs" prefix="user2_log." suffix=".txt"  timestamp="true"/>
            <context path="" docBase="/home/user2/www" reloadable="true" caseSensitive="false" debug="0"></context>
      </host>

配置nginx域名转发：

设置nginx.conf不修改Host等字段

location / {
            proxy_pass   http://127.0.0.1:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

设置shadowsocks多用户：增加/etc/shadowsocks.json的配置项

  "port_password":{
      "8484":"password1",
      "8585":"password2"
  },