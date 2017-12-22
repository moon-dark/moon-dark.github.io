---
title: Nginx安装与配置
date: 2017-12-22 09:45:40
tags:
  - code
  - Nginx
---

### 安装
```
sudo apt-get update
sudo apt-get install nginx
```

查看状态：
```
systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2017-12-22 09:29:14 CST; 19min ago
 Main PID: 5072 (nginx)
   CGroup: /system.slice/nginx.service
           ├─5072 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           └─5073 nginx: worker process

Dec 22 09:29:14 iZwz9e5k6k35vend0wprhqZ systemd[1]: Starting A high performance web server and a reverse proxy server...
Dec 22 09:29:14 iZwz9e5k6k35vend0wprhqZ systemd[1]: nginx.service: Failed to read PID from file /run/nginx.pid: Invalid argument
Dec 22 09:29:14 iZwz9e5k6k35vend0wprhqZ systemd[1]: Started A high performance web server and a reverse proxy server.

```
<!-- more -->

查看IP地址
```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

访问http://server_domain_or_IP看到如下默认页面，则nginx已经安装好
![](https://assets.digitalocean.com/articles/nginx_1604/default_page.png)

### 管理
```
#服务命令：
systemctl stop nginx          #停止nginx服务
systemctl start nginx         #启动nginx服务
systemctl restart nginx       #重启nginx服务
systemctl reload nginx        #重新加载配置文件
systemctl disable nginx       #禁用nginx服务，开机不自动重启
systemctl enable nginx        #启动nginx服务，开机自动重启

#直接命令
nginx
nginx -s [stop/quit/reopen/reload]
```

### 配置
主进程信息
```
florin@vm:~$ ps aux | grep nginx
root       884  0.0  0.0  76944  1308 ?        Ss   10:28   0:00 nginx: master process /usr/sbin/nginx
```
详细信息
```
$ /usr/sbin/nginx -V 2>&1 | grep --colour=auto conf
```
将给出类似 --conf-path=/etc/nginx/nginx.conf 的配置位置

配置nginx域名转发（80端口转发9091）：
```
server {
     listen       80;
     server_name nas2.yaoping.win;

     error_page   500 502 503 504  /50x.html;
     location = /50x.html {
         root   html;
     }

     location / {
        proxy_pass   http://127.0.0.1:9091;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     }
 }
```

配置nginx子域名：
```
# 负载均衡，根据IP哈希决定访问的服务器/端口
upstream  apiproxy {
   ip_hash;
   server     api.yaoping.win max_fails=3 fail_timeout=5s weight=5;
}

server {
    listen       80;
    server_name api.yaoping.win;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }

    location / {
        root   /opt/api.yaoping.win;
        index  index.html index.htm;
    }

    location /v1/ {
        root   html;
        index  index.html index.htm;
        proxy_pass  http://apiproxy ;

       # 跨域设置
       if ($http_origin ~* (https?://192\.168\.1\.[0-9]+(:[0-9]+)?|https?://localhost(:[0-9]+)?)|https?://127\.0\.0\.1(:[0-9]+)?|https?://api\.yaoping\.win(:[0-9]+)?) {
           add_header 'Access-Control-Allow-Origin' "$http_origin";
       }
    }
}
```
