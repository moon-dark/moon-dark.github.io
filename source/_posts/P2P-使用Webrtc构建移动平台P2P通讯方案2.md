---
title: '使用Webrtc构建移动平台P2P通讯方案: 信令服务端(Signaling server)'
date: 2018-01-02 14:25:38
tags:
  - code
---

### [Realtime communication with WebRTC](https://codelabs.developers.google.com/codelabs/webrtc-web/)
Gitbub工程[webrtc-web](https://github.com/googlecodelabs/webrtc-web/)

**初始化**
```
npm init

#安装依赖
npm i socket.io
npm i node-static
npm i os
```
<!-- more -->
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
