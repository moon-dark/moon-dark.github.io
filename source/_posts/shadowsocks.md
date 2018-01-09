---
title: shadowsocks
date: 2018-01-04 11:01:13
tags:
  - wall
---
[在 CentOS 7 下安装配置 shadowsocks](http://morning.work/page/2015-12/install-shadowsocks-on-centos-7.html)
[CentOS下pip和shadowsocks的安装使用](https://teddysun.com/339.html)

### 安装配置 shadowsocks
**安装 pip**
```
curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
python get-pip.py
```

**安装配置 shadowsocks**
```
pip install --upgrade pip
pip install shadowsocks

# 升级
pip install https://github.com/shadowsocks/shadowsocks/archive/master.zip

# 1、安装package
pip install shadowsocks
# 2、列出已安装的packages
pip freeze
# 3、安装特定版本的package
pip install shadowsocks=1.3.3′
pip install shadowsocks>1.0,<1.3.3′
# 4、升级已安装的package到最新版本
pip install -U shadowsocks
# 5、卸载已安装的package
pip uninstall shadowsocks
# 6、查询已安装的package
pip search “shadowsocks”
```
<!-- more -->
**提高 Shadowsocks 的性能:**<br>
安装 gevent可以提高 Shadowsocks 的性能。CentOS下安装gevent依赖libevent和greenlet。
```
# 安装libevent：
yum install -y libevent
# 安装greenlet：
pip install greenlet
# 安装gevent：
pip install gevent
```

**配置文件/etc/shadowsocks.json：**
```
{
  "server": "0.0.0.0",
  "server_port": 8388,
  "password": "uzon57jd0v869t7w",
  "method": "aes-256-cfb"
}
```
说明：
- method为加密方法，可选aes-128-cfb, aes-192-cfb, aes-256-cfb, bf-cfb, cast5-cfb, des-cfb, rc4-md5, chacha20, salsa20, rc4, table
- server_port为服务监听端口
- password为密码，可使用密码生成工具生成一个随机密码
以上三项信息在配置 shadowsocks 客户端时需要配置一致

### 配置自启动
新建启动脚本文件/etc/systemd/system/shadowsocks.service，内容如下：
```
[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

[Install]
WantedBy=multi-user.target
```
执行以下命令启动 shadowsocks 服务：
```
systemctl enable shadowsocks
systemctl start shadowsocks
```
为了检查 shadowsocks 服务是否已成功启动，可以执行以下命令查看服务的状态：
```
systemctl status shadowsocks -l
```
如果服务启动成功，则控制台显示的信息可能类似这样：
```
● shadowsocks.service - Shadowsocks
   Loaded: loaded (/etc/systemd/system/shadowsocks.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2015-12-21 23:51:48 CST; 11min ago
 Main PID: 19334 (ssserver)
   CGroup: /system.slice/shadowsocks.service
           └─19334 /usr/bin/python /usr/bin/ssserver -c /etc/shadowsocks.json

Dec 21 23:51:48 morning.work systemd[1]: Started Shadowsocks.
Dec 21 23:51:48 morning.work systemd[1]: Starting Shadowsocks...
Dec 21 23:51:48 morning.work ssserver[19334]: INFO: loading config from /etc/shadowsocks.json
Dec 21 23:51:48 morning.work ssserver[19334]: 2015-12-21 23:51:48 INFO     loading libcrypto from libcrypto.so.10
Dec 21 23:51:48 morning.work ssserver[19334]: 2015-12-21 23:51:48 INFO     starting server at 0.0.0.0:8388
```

### 一键安装脚本
直接执行以下命令从 GitHub 下载安装脚本并执行：
```
bash <(curl -s http://morning.work/examples/2015-12/install-shadowsocks.sh)
```
