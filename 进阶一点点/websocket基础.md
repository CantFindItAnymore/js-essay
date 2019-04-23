### 一. websocket是啥？

一种网络通信协议，与http类似。

### 二. 为嘛需要websocket？

有了http协议，还要websocket干嘛？

因为http协议只能由客户端发起通信，若服务器有连续的状态变化，客户端就只能使用 “轮询” 来处理了。

这样做效率很低并且浪费资源。

所以，websocket就诞生了。

### 三. websocket的特点

- 服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息。是一种双向的平等的对话。
- 建立在TCP之上。
- 与http有良好的兼容性。
- 数据格式轻量，性能nice。
- 没有同源策略，随便浪。
- 协议标识符是ws/wss，如`ws://example.com:80/some/path`

### 四. websocket的用法

```javascript
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};      
```

### 五. 客户端的API

#### 1. WebSocket构造函数

```javascript
var ws = new WebSocket("wss://echo.websocket.org");
```

这句代码用于新建websocket实例。执行后，客户端就会与服务器进行连接。

#### 2. websocket.readyState

返回实例对象的当前状态，4种↓

- CONNECTING：值为0，表示正在连接。 
- OPEN：值为1，表示连接成功，可以通信了。 
- CLOSING：值为2，表示连接正在关闭。 
- CLOSED：值为3，表示连接已经关闭，或者打开连接失败。 

栗子↓

```javascript
switch (ws.readyState) {
  case WebSocket.CONNECTING:
    // do something
    break;
  case WebSocket.OPEN:
    // do something
    break;
  case WebSocket.CLOSING:
    // do something
    break;
  case WebSocket.CLOSED:
    // do something
    break;
  default:
    // this never happens
    break;
}
```

#### 3. webSocket.onopen

用于指定连接成功后的回调函数。

```javascript
ws.onopen = function () {
  ws.send('Hello Server!');
}
```

如果要指定多个回调函数，可以使用`addEventListener`方法。 

```javascript
ws.addEventListener('open', function (event) {
  ws.send('Hello Server!');
});
```

#### 4. webSocket.onclose

用于指定连接关闭时的回调函数。

```javascript
ws.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
};

ws.addEventListener("close", function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
});
```

#### 5. webSocket.onmessage

用于指定客户端收到服务器数据后的回调函数。

注意，服务器数据可能是文本，也可能是二进制数据（`blob`对象或`Arraybuffer`对象）。 

```javascript
ws.onmessage = function(event){
  if(typeof event.data === string) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```

#### 6. webSocket.send()

用于向服务器发送数据。

```javascript
// 文本
ws.send('your message');

// Blob
var file = document.querySelector('input[type="file"]').files[0];
ws.send(file);

//ArrayBuffer
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
ws.send(binary.buffer);
```

#### 7. webSocket.bufferedAmount

实例对象的`bufferedAmount`属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。 

```javascript
var data = new ArrayBuffer(10000000);
socket.send(data);

if (socket.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```

#### 8. webSocket.onerror

用于指定报错时的回调函数。 

```javascript
socket.onerror = function(event) {
  // handle error event
};

socket.addEventListener("error", function(event) {
  // handle error event
});
```

### 六. 服务端的实现

node实现

- Socket.IO
- Websocket-Node

### 七. 创建一个webSocket服务的工具—WebSocketd



