---
title: '使用Webrtc构建移动平台P2P通讯方案：WebRTC文件传输'
date: 2018-01-02 15:38:36
tags:
  - code
---
# [WebRTC code samples](https://github.com/webrtc/samples)
[WebRTC samples Transfer a file](https://webrtc.github.io/samples/src/content/datachannel/filetransfer/)
### [WebRTC data channels](https://www.html5rocks.com/en/tutorials/webrtc/datachannels/)

### **初始化**
```
npm install webrtc-adapter socket.io-client
```

### 代码解析
**配置TURN/STUN服务器**
```javascript
const TYPE_WEB = 0
const TYPE_APP = 1

var dcctrl = {  // datachannel control
  configuration: {
    'iceServers': [{
      'urls': ['stun:stun.xten.com:3478', '......']
    }]
  },
  socket: Object,
  isInitiator: false,
  timestampStart: 0,
  prevTime: 0
}
```
<!-- more -->
**连接信令服务器**
```javascript
loginWS () {
  dcctrl.socket = io('http://192.168.2.9:3186')
  var pThis = this
  webrtc.sendMessage = function (message) {
    console.log('Client sending message: ', message)
    dcctrl.socket.emit('message', message)
  }
  dcctrl.socket.on('connect', this.connect)
  dcctrl.socket.on('disconnect', this.disconenct)
  dcctrl.socket.on('syncfrom', this.syncfrom)
  dcctrl.socket.on('syncto', this.syncto)
  dcctrl.socket.on('export', this.ws_export)
  dcctrl.socket.on('exception', this.exception)
  dcctrl.socket.on('byebye', this.byebye)
  dcctrl.socket.on('message', webrtc.signalingMessageCallback)
  dcctrl.socket.on('ipaddr', this.ipaddr)
  dcctrl.socket.on('transfile', webrtc.initSend)
  dcctrl.socket.on('ctrlSendSpeed', ctrlSendSpeed)
}
```

**初始化P2P连接**
```javascript
syncfrom () {
  dcctrl.socket.emit('syncto', this.blogData)
  // create new datachannel
  dcctrl.isInitiator = true
  webrtc.createPeerConnection(dcctrl.isInitiator, dcctrl.configuration)
},
syncto (data) {
  $('.bottom_area').css('display', 'block')
  this.blogData = data  //保存数据

  dcctrl.isInitiator = false
  webrtc.createPeerConnection(dcctrl.isInitiator, dcctrl.configuration)
},
```

**webrtc.js  处理P2P业务逻辑**<br>
fork from [googlecodelabs/webrtc-web/step-06/js/main.js](https://github.com/googlecodelabs/webrtc-web/blob/master/step-06/js/main.js)

```javascript
import adapter from 'webrtc-adapter'
var peerConn
var dataChannel

function signalingMessageCallback (message) {
  if (message.type === 'offer') {
    console.log('Got offer. Sending answer to peer.')
    peerConn.setRemoteDescription(new window.RTCSessionDescription(message), function () {},
      logError)
    peerConn.createAnswer(onLocalSessionCreated, logError)
  } else if (message.type === 'answer') {
    console.log('Got answer.')
    peerConn.setRemoteDescription(new window.RTCSessionDescription(message), function () {},
      logError)
  } else if (message.type === 'candidate') {
    peerConn.addIceCandidate(new window.RTCIceCandidate({
      candidate: message.candidate
    }))
  } else if (message === 'bye') {
  // TODO: cleanup RTC connection?
  }
}

function createPeerConnection (isInitiator, config) {
  console.log('Creating Peer connection as initiator?', isInitiator, 'config:',
    config)
  peerConn = new window.RTCPeerConnection(config)

  // send any ice candidates to the other peer
  peerConn.onicecandidate = function (event) {
    console.log('icecandidate event:', event)
    if (event.candidate) {
      webrtc.sendMessage({
        type: 'candidate',
        label: event.candidate.sdpMLineIndex,
        id: event.candidate.sdpMid,
        candidate: event.candidate.candidate
      })
    } else {
      console.log('End of candidates.')
    }
  }

  if (isInitiator) {
    console.log('Creating Data Channel')
    dataChannel = peerConn.createDataChannel('vbzdatachannel-' + Math.floor(Math.random() * 100000).toString(10))
    onDataChannelCreated(dataChannel)

    console.log('Creating an offer')
    peerConn.createOffer(onLocalSessionCreated, logError)
  } else {
    peerConn.ondatachannel = function (event) {
      console.log('ondatachannel:', event.channel)
      dataChannel = event.channel
      onDataChannelCreated(dataChannel)
    }
  }
}

function onLocalSessionCreated (desc) {
  console.log('local session created:', desc)
  peerConn.setLocalDescription(desc, function () {
    console.log('sending local desc:', peerConn.localDescription)
    webrtc.sendMessage(peerConn.localDescription)
  }, logError)
}

function onDataChannelCreated (channel) {
  console.log('onDataChannelCreated:', channel)

  channel.onopen = function () {
    console.log('CHANNEL opened!!!')
  }

  channel.onmessage = (adapter.browserDetails.browser === 'firefox') ? receiveDataFirefoxFactory() : receiveDataChromeFactory()
}

function receiveDataChromeFactory () {
  // var buf

  return function onmessage (event) {
    var data = new Uint8ClampedArray(event.data)
    webrtc.buf.set(data, webrtc.currentCount)

    webrtc.currentCount += data.byteLength
    // console.log('count: ' + webrtc.currentCount)
    webrtc.ctrlSendSpeed(webrtc.currentCount)

    if (webrtc.currentCount === webrtc.buf.byteLength) {
      // we're done: all data chunks have been received
      console.log('Done. Rendering photo.')
      webrtc.renderPhoto(webrtc.buf)
    }
  }
}

function receiveDataFirefoxFactory () {
  // var parts

  return function onmessage (event) {
    webrtc.parts.push(event.data)
    webrtc.currentCount += event.data.size
    console.log('Got ' + event.data.size + ' byte(s), ' + (webrtc.fileLength - webrtc.currentCount) +
      ' to go.')
    webrtc.ctrlSendSpeed(webrtc.currentCount)

    if (webrtc.currentCount === webrtc.fileLength) {
      console.log('Assembling payload')
      var buf = new Uint8ClampedArray(webrtc.fileLength)
      var compose = function (i, pos) {
        var reader = new window.FileReader()
        reader.onload = function () {
          buf.set(new Uint8ClampedArray(webrtc.result), pos)
          if (i + 1 === webrtc.parts.length) {
            console.log('Done. Rendering photo.')
            webrtc.renderPhoto(buf)
          } else {
            compose(i + 1, pos + webrtc.result.byteLength)
          }
        }
        reader.readAsArrayBuffer(webrtc.parts[i])
      }
      compose(0, 0)
    }
  }
}

function logError (err) {
  console.log(err.toString(), err)
}

async function sendFile (data) {
  // Split data channel message in chunks of this byte length.
  var CHUNK_LEN = 16384
  var len = data.byteLength
  var n = len / CHUNK_LEN | 0

  // console.log('Sending a total of ' + len + ' byte(s)')

  var sliceFile = function (i) {
    var start = i * CHUNK_LEN
    var end = (i + 1) * CHUNK_LEN
    // console.log(i + ' : ' + start + ' - ' + (end - 1))
    dataChannel.send(data.slice(start, end))
    webrtc.sendDataLen = end
    if (i < n) {
      window.setTimeout(sliceFile, webrtc.waitime, i + 1)
    } else {
      if (len % CHUNK_LEN) {
        dataChannel.send(data.slice(n * CHUNK_LEN))
      }
    }
  }
  sliceFile(0)
}

function initSend (data) {
  console.log('initSend: ' + data.name + ', ' + data.size)
  webrtc.fileLength = data.size
  webrtc.currentCount = 0
  webrtc.buf = window.buf = new Uint8ClampedArray(webrtc.fileLength)
  webrtc.parts = []
  webrtc.filename = data.name
}

var webrtc = {
  props: {
    sendMessage: Function,
    renderPhoto: Function,
    ctrlSendSpeed: Function,
    filename: String,
    fileLength: 0,
    waitime: 0,
    sendDataLen: 0
  },
  signalingMessageCallback,
  createPeerConnection,
  sendFile,
  initSend
}

export default webrtc
```

**文件导入导出**
```javascript
<div class="bottom_area">
  <div @click="vb_import()" class="ui_button ui_button_primary btn_import" >导入</div>
  <div @click="vb_export()" class="ui_button ui_button_primary btn_export" >导出</div>
  <input id="file-input" type="file" name="name" style="display: none;" @change="sendFileEvent($event)"/>
</div>

vb_import () {
  $('#file-input').trigger('click')
  dcctrl.timestampStart = (new Date()).getTime()
},
vb_export () {
  dcctrl.socket.emit('export', this.exportlist)
  dcctrl.timestampStart = (new Date()).getTime()
},
sendFileEvent (event) {
  var files = event.target.files
  if (files && files[0]) {
    this.sendFile(files[0])
  }
},
sendFile (file) {
  console.log(file.name)
  if (file) {
    var reader = new window.FileReader()
    reader.onload = function (e) {
      // ready to transfer file
      webrtc.fileLength = e.target.result.byteLength
      dcctrl.socket.emit('transfile', {size: webrtc.fileLength, name: file.name})

      // this.status = STATUS_IMPORT
      webrtc.sendFile(e.target.result)
    }
    reader.readAsArrayBuffer(file)
  }
},
saveToDisk (fileUrl, fileName) {
  var save = document.createElement('a')
  save.href = fileUrl
  save.target = '_blank'
  save.download = fileName || fileUrl
  save.click()
},
webrtc.renderPhoto = function (data) {
  console.log('renderPhoto: !!!')
  pThis.speedconfig.visible = false
  var binaryData = []
  binaryData.push(data)
  if (pThis.type === TYPE_APP) {
    try {
      // send data to App
      window.sync.importData(data)         //小文件可以写入，大文件会导致APP发生OOM；使用原生接口实现WebRTC的P2P功能以避免此问题
    } catch (e) {
      pThis.setTip({text: 'Java exception'})
      dcctrl.socket.emit('exception', 'Java exception')
    }
  } else {
    var url = window.URL.createObjectURL(new window.Blob(binaryData, {type: '*/*'}))
    pThis.saveToDisk(url, webrtc.filename)
  }
  data = []
},
saveToDisk (fileUrl, fileName) {
  var save = document.createElement('a')
  save.href = fileUrl
  save.target = '_blank'
  save.download = fileName || fileUrl
  save.click()
},
```
