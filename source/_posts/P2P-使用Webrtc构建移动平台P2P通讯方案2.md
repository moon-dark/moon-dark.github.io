---
title: '使用Webrtc构建移动平台P2P通讯方案: 信令服务端(Signaling server)'
date: 2018-01-02 14:25:38
tags:
  - code
  - webrtc
---

Socket.IO 是一个面向实时 web 应用的 JavaScript 库。它使得服务器和客户端之间实时双向的通信成为可能。他有两个部分：在浏览器中运行的客户端库，和一个面向Node.js的服务端库。两者有着几乎一样的API。像Node.js一样，它也是事件驱动的.

Socket.IO 主要使用WebSocket协议。但是如果需要的话，Socket.io可以回退到几种其它方法，例如Adobe Flash Sockets，JSONP拉取，或是传统的AJAX拉取，并且在同时提供完全相同的接口。尽管它可以被用作WebSocket的包装库，它还是提供了许多其它功能，比如广播至多个套接字，存储与不同客户有关的数据，和异步IO操作。

**socket.io**  [链接](https://github.com/socketio/socket.io)<br>
socket.io支持Java，C++，Swift语言<br>

- 跨平台支持：
![](https://camo.githubusercontent.com/fface5a8523859ace9b349fc8922af0a8d6941f4/68747470733a2f2f73617563656c6162732e636f6d2f62726f777365722d6d61747269782f736f636b65742e737667)

- 安装：
```
npm install socket.io --save
```

- 使用：
```
var server = require('http').createServer();
var io = require('socket.io')(server);
io.on('connection', function(client){
  client.on('event', function(data){});
  client.on('disconnect', function(){});
});
server.listen(3000);
```

### 文档
- [Realtime communication with WebRTC](https://codelabs.developers.google.com/codelabs/webrtc-web/)
- [webrtc-web](https://github.com/googlecodelabs/webrtc-web/)
- [官方网站](https://socket.io/)

<!-- more -->

**初始化**
```
npm init

#安装依赖
npm i socket.io
npm i node-static
npm i os
```

**代码server.js**
```javascript
'use strict';

var os = require('os');
var server = require('http').createServer();
var io = require('socket.io')(server, {origins:'localhost:* http://localhost:* http://192.168.2.33:*'});       // 设置跨域请求，允许localhost和内网地址192.168.2.33访问

var TYPE_WEB = 0;
var TYPE_APP = 1;
var users = {};  // uid : {wid: '', wsock: Object, appid: '', asock: Object}
var sockmap = {};  // socet: uid
io.on('connection', function(client){
  client.emit('welcome', { message: 'Welcome!', id: client.id });
  client.on('register', function(data) {
    var uid = data.uid;
    var type = data.type;

    console.log('==>register : ' + uid + ' from ' + (type == TYPE_WEB ? 'web' : 'app'));
    sockmap[client] = uid

    if( !users[uid] ) {
      if(type == TYPE_WEB)
        users[uid] = {wid: uid, wsock: client}
      else if(type == TYPE_APP)
        users[uid] = {appid: uid, asock: client}
    }
    else {
      if( type == TYPE_WEB) {
        if(users[uid]['wid'] && users[uid]['wid'] != uid) {
          logout( users[uid]['wid'] )
        }

        users[uid]['wid'] = uid
        users[uid]['wsock'] = client
      } else if( type == TYPE_APP) {
        if(users[uid]['appid'] && users[uid]['appid'] != uid) {
            logout( users[uid]['appid'] )
        }

        users[uid]['appid'] = uid
        users[uid]['asock'] = client
      }

      if(users[uid]['wid'] && users[uid]['appid']) {
        // start sync
        // console.log( 'sync ' + uid );
        users[uid]['asock'].emit('syncfrom');
      }
    }
  });

  // App端发送同步数据，客户端接收数据
  client.on('syncto', function(data) {
    // console.log( 'sync to web ');
    var uid = sockmap[client]
    if(!users[uid]) return
    users[uid]['wsock'].emit('syncto', data);
  });

  client.on('disconnect', function(){
    var uid = sockmap[client]
    if(users[uid]) {
        if(users[uid]['wsock'] == client) {
            console.log( uid + ' disconnect from web')
            if(users[uid]['asock']) {
                users[uid]['asock'].emit('byebye')
                delete users[uid]['wid']
                delete users[uid]['wsock']
            }
            else {
                delete users[uid]
            }
        }
        else if(users[uid]['asock'] == client) {
            console.log( uid + ' disconnect from app')
            if(users[uid]['wsock']) {
                users[uid]['wsock'].emit('byebye')
                delete users[uid]['appid']
                delete users[uid]['asock']
            }
            else {
                delete users[uid]
            }
        }
    }

    delete sockmap[client]

    console.log( '<==')
  });

  client.on('export', function(data) {
    // send data to web
    var uid = sockmap[client]
    if(!users[uid]) return
    var targetClient = (client === users[uid]['asock'] ? users[uid]['wsock'] : users[uid]['asock'])
    targetClient.emit('export', data);
  });

  // 异常响应
  client.on('exception', function(data) {
    // console.log( 'exception ' );
    // send data to web
    var uid = sockmap[client]
    if(!users[uid]) return
    var targetClient = (client === users[uid]['asock'] ? users[uid]['wsock'] : users[uid]['asock'])
    targetClient.emit('exception', data);
  });

  client.on('ipaddr', function() {
    var ifaces = os.networkInterfaces();
    for (var dev in ifaces) {
      ifaces[dev].forEach(function(details) {
        if (details.family === 'IPv4' && details.address !== '127.0.0.1') {
          client.emit('ipaddr', details.address);
          console.log('ipaddr = ', details.address)
        }
      });
    }
  });

  client.on('message', function(message) {
    var uid = sockmap[client]
    if(!users[uid]) return
    var targetClient = (client === users[uid]['asock'] ? users[uid]['wsock'] : users[uid]['asock'])
    targetClient.emit('message', message);
  });

  // 数据发送端传递文件长度和文件名
  client.on('transfile', function(data) {
    var uid = sockmap[client]
    if(!users[uid]) return
    var targetClient = (client === users[uid]['asock'] ? users[uid]['wsock'] : users[uid]['asock'])
    targetClient.emit('transfile', {size: data.size, name: data.name});
  });

  // 数据接收端发出事件ctrlSendSpeed，传递已接收数据；数据发送端控制发送速率
  client.on('ctrlSendSpeed', function(length) {
    var uid = sockmap[client]
    if(!users[uid]) return
    var targetClient = (client === users[uid]['asock'] ? users[uid]['wsock'] : users[uid]['asock'])
    targetClient.emit('ctrlSendSpeed',length);
  });

});
console.log( 'listening :3186 ...');
server.listen(3186);            // 随机未占用端口

```

**执行**
```
node server.js
```
