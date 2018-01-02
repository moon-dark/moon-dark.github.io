---
title: '使用Webrtc构建移动平台P2P通讯方案: turn服务端'
date: 2018-01-02 13:53:28
tags:
  - code
---

# turn服务端
rfc5766-turn-server是谷歌推荐的turn开源项目，经常作WebRTC的服务器端使用。

[coturn TURN server project](https://github.com/coturn/coturn)
[下载](https://github.com/coturn/coturn/wiki/Downloads)


### 编译环境搭建
[编译rfc5766-turn-server搭建turn服务器](http://www.hankcs.com/program/network/compile-rfc5766-turn-server-to-build-turn-server.html)

** 安装编译工具链 **
```
sudo apt-get install build-essential

#确认安装成功：
$ gcc -v
$ make -v

#安装依赖项
sudo apt-get install libssl-dev libevent-dev libpq-dev mysql-client libmysqlclient-dev libhiredis-dev
```
<!-- more -->
**检出代码**
```
git clone https://github.com/coturn/coturn.git
```

**编译**
```
./configure
make
sudo make install
```

**测试**
```
[root@MyServer coturn]# turnserver -h
0: log file opened: /var/log/turn_3659_2018-01-02.log
0:
RFC 3489/5389/5766/5780/6062/6156 STUN/TURN Server
Version Coturn-4.5.0.8 'dan Eider'
0:
Max number of open files/sockets allowed for this process: 4096
0:
Due to the open files/sockets limitation,
max supported number of TURN Sessions possible is: 2000 (approximately)
0:

==== Show him the instruments, Practical Frost: ====

0: TLS supported
0: DTLS supported
0: DTLS 1.2 is not supported
0: TURN/STUN ALPN is not supported
0: Third-party authorization (oAuth) supported
0: GCM (AEAD) supported
0: OpenSSL compile-time version: OpenSSL 1.0.1e-fips 11 Feb 2013 (0x1000105f)
0:
0: SQLite is not supported
0: Redis is not supported
0: PostgreSQL is not supported
0: MySQL supported
0: MongoDB is not supported
0:
0: Default Net Engine version: 3 (UDP thread per CPU core)

=====================================================


Usage: turnserver [options]
Options:
 -d, --listening-device <device-name>           Listener interface device (NOT RECOMMENDED. Optional, Linux only).
 -p, --listening-port           <port>          TURN listener port (Default: 3478).
                                                Note: actually, TLS & DTLS sessions can connect to the "plain" TCP & UDP port(s), too,
                                                if allowed by configuration.
 --tls-listening-port           <port>          TURN listener port for TLS & DTLS listeners
                                                (Default: 5349).
                                                Note: actually, "plain" TCP & UDP sessions can connect to the TLS & DTLS port(s), too,

略...
```

**支持协议**<br/>
测试各基础协议是否支持
```
[root@MyServer coturn]# cd examples/
[root@MyServer examples]# ./scripts/rfc5769.sh
RFC 5769 message fingerprint test(0) result: success
RFC 5769 simple request short-term credentials and integrity test result: success
RFC 5769 NEGATIVE fingerprint test(0) result: success
RFC 5769 message structure, long-term credentials and integrity test result: success
RFC 5769 message encoding test result: success
RFC 5769 NEGATIVE long-term credentials test result: success
RFC 5769 message fingerprint test(1) result: success
RFC 5769 IPv4 response short-term credentials and integrity test result: success
RFC 5769 NEGATIVE fingerprint test(1) result: success
RFC 5769 IPv4 encoding result: success
RFC 5769 message fingerprint test(2) result: success
RFC 5769 IPv6 response short-term credentials and integrity test result: success
RFC 5769 NEGATIVE fingerprint test(2) result: success
RFC 5769 IPv6 encoding result: success
oauth token A128GCM:OK
oauth token A256GCM:OK
[root@MyServer examples]#

```

**TURN测试**<br/>
在tmux或screen里面执行命令./scripts/basic/relay.sh
![](/img/coturn.png)

然后执行命令：./scripts/basic/udp_c2c_client.sh，把客户端跑起来，可以看到它在通过中转服务器自己给自己发数据：
![](/img/coturn2.png)

至此，说明一切完全正常。

### 配置
[实战rfc5766-turn-server和ice4j广域网通讯](http://www.hankcs.com/program/network/actual-rfc5766-turn-server-and-ice4j-wide-area-network-communication.html)

rfc5766-turn-server是利用配置文件来定义自己的功能表现的，安装完成后，在下列路径都有默认的配置文件
```
/usr/local/etc/turnserver.conf.default
rfc5766-turn-server检出目录/examples/etc/turnserver.conf
```
配置项很多，但是初级用户用得上的只有两个。
- 配置外网IP
在配置文件中加入一句 external-ip=<服务器的外网IP>, 3478是turnserver服务占用的端口<br/>
- 配置用户名和密码
在产品级的场景中，rfc5766-turn-server支持数据库和动态增删用户。而在我的这种实验中，我选择静态定义两个用户，在配置文件中加入:
```
user=u1:p1
user=u2:p2
```
这代表两个用户，遵从 用户名:密码 的格式。<br/>

**启动rfc5766-turn-server**
```
sudo turnserver -c /你的路径/turnserver.conf
```

### 国内免费可用的STUN服务器(webrtc 必备）
[国内免费可用的STUN服务器(webrtc 必备）](http://www.cnblogs.com/idignew/p/7341479.html)

NO.  |   STUN服务器 |  端口  |  有效测试日期
--- | --- | --- | --- | ---
1 | stun.xten.com | 3478 | 2017/08/14
2 | stun.voipbuster.com | 3478 | 2017/08/14
3 | stun.voxgratia.org | 3478 | 2017/08/14
4 | stun.sipgate.net | 10000 | 2017/08/14
5 | stun.ekiga.net | 3478 | 2017/08/14
6 | stun.ideasip.com | 3478 | 2017/08/14
7 | stun.schlund.de | 3478 | 2017/08/14
8 | stun.voiparound.com | 3478 | 2017/08/14
9 | stun.voipbuster.com | 3478 | 2017/08/14
10 | stun.voipstunt.com | 3478 | 2017/08/14
11 | numb.viagenie.ca | 3478 | 2017/08/14
12 | stun.counterpath.com | 3478 | 2017/08/14
13 | stun.1und1.de | 3478 | 2017/08/14
14 | stun.gmx.net | 3478 | 2017/08/14
16 | stun.callwithus.com | 3478 | 2017/08/14
17 | stun.counterpath.net | 3478 | 2017/08/14
18 | stun.internetcalls.com | 3478 | 2017/08/14
19 | stun.voip.aebc.com | 3478 | 2017/08/14
20 | numb.viagenie.ca | 3478 | 2017/08/14
