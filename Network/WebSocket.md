# WebSocket

## WebSocket
### 概述
- 客户端向 WebSocket 服务器通知（notify）一个带有所有 接收者ID（recipients IDs） 的事件（event），服务器接收后立即通知所有活跃的（active）客户端，只有ID在接收者ID序列中的客户端才会处理这个事件。
- WebSocket 是全双工（双方同时进行双向通信）
- 建立在 TCP 协议之上，服务器端的实现比较容易。
- 与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443 ，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
- 数据格式比较轻量，性能开销小，通信高效。
- 可以发送文本，也可以发送二进制数据。
- 没有同源限制，客户端可以与任意服务器通信。
- 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。
- WebSocket 客户端、服务端的最小单位是数据帧(frame)，一个或多个数据帧组成完整的消息(message)
### 经过
- 协议有两部分：握手和数据传输。
- 握手
  * 客户端：申请协议升级
    ```js
    GET /chat HTTP/1.1                           // 必须是GET方法
    Host: server.example.com                     // 
    Upgrade: websocket                           // 升级到 WebSocket 协议
    Connection: Upgrade                          // 表示升级协议
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==  // 与后面服务端响应首部的 Sec-WebSocket-Accept 是配套的，提供基本的防护(避免恶意/无意连接)
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13                    // 表示 websocket 的版本。如果服务端不支持该版本，需要返回一个 Sec-WebSocket-Versionheader ，里面包含服务端支持的版本号。
    ```
  * 响应协议升级
    ```js
    HTTP/1.1 101 Switching Protocols                        // 101 表示协议切换
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=      // 根据客户端请求首部的 Sec-WebSocket-Key 计算出来
    Sec-WebSocket-Protocol: chat
    ```
### 数据帧
WebSocket 客户端、服务端通信的最小单位是 帧（frame），由 1 个或多个帧组成一条完整的 消息（message）。
* 发送端：将消息切割成多个帧，并发送给服务端；
* 接收端：接收消息帧，并将关联的帧重新组装成完整的消息；

### 连接保持+心跳
WebSocket 为了保持客户端、服务端的实时双向通信，需要确保客户端、服务端之间的 TCP 通道保持连接没有断开。然而，对于长时间没有数据往来的连接，如果依旧长时间保持着，可能会浪费包括的连接资源。但不排除有些场景，客户端、服务端虽然长时间没有数据往来，但仍需要保持连接。这个时候，可以采用心跳来实现。
- 发送方 ->接收方：ping
- 接收方 ->发送方：pong

## 例子
后端部分
```js
const WebSocket = require('ws')
const WebSocketServer = WebSocket.Server
const wss = new WebSocketServer({
  port: 3001
})
wss.on('connection', function (ws) {
  ws.on('message', function (data) {
    wss.broadcast(JSON.stringify(data))
  })
})
wss.broadcast = function (data) { // 广播
  wss.clients.forEach(function (client) {
    client.send(data)
  })
}
```
前端部分
```html
<!doctype html>
<html>
<head>
  <title>WS</title>
  <style>
    html {margin: 0; height: 100%;}
    body {margin: 0; height: 100%; display: flex; flex-flow: row wrap;}
    .content {height: 80%;flex-basis: 70%;overflow-y: auto;background: #333;}
    .user {height: 80%;flex-basis: 30%;background: #666;}
    .bottom {height: 20%;flex-basis: 100%;background: #999;}
  </style>
</head>
<body>
  <div class="content"></div>
  <div class="user"></div>
  <div class="bottom">
    <span>你的临时ID:</span>
    <span class="uid"></span><br/>
    <span>发送消息：</span>
    <input type="textarea" name="textarea" />
    <input type="button" name="button" value="提交" />
  </div>
  <script>
    let uid = getRandomUid(8)
    let uidElement = document.getElementsByClassName("uid")[0]
    let contentElement = document.getElementsByClassName("content")[0]
    let userElement = document.getElementsByClassName("user")[0]
    let textarea = document.getElementsByName("textarea")[0]
    let button = document.getElementsByName("button")[0]
    let sendMessage = {
      "uid": uid,
      "message": "", 
    }
    let ws = new WebSocket('ws://localhost:3001/test')
    uidElement.innerHTML = uid // 用户临时id
    ws.addEventListener("open", () => { // 登录信息:发送uid到服务器
      ws.send(JSON.stringify(sendMessage))
    })
    button.addEventListener("click", () => { // 提交消息到服务器
      sendMessage.message = textarea.value
      ws.send(JSON.stringify(sendMessage))
    })
    
    ws.onmessage = function (res) { // 监听服务器返回的消息
      addMsg2Content(JSON.parse(JSON.parse(res.data)))
    }

    function addMsg2Content (data) { // 添加聊天信息
      if (data.message) {
        let p = document.createElement("p")
        p.appendChild(document.createTextNode(`${data.uid}: ${data.message}`))
        contentElement.appendChild(p)
      }
    }
    function getRandomUid (len) { // 获取随机uid
      let str = "123456789abcdef"
      let result = ""
      for (;len > 0;len--) {
        result += str.charAt(Math.floor(Math.random() * 16))
      }
      return result
    }
  </script>
</body>
</html>
```

## 服务端主动通信or保持连接
### 后端接口
```js
let i = 0
app.get("/keep", function (req, res) {
  i++
  res.end("delay to send message: " + i)
})
```
### 轮询(传统)
- 通过`setInterval`或者`setTimeout`重复发起请求
- `setInterval`的缺陷：因为网络是波动的,导致浏览器接受的数据的先后到达顺序可能与服务器发送顺序不一致  
  解决方法是`setTimeout`
  ```js
  setInterval(() => {
    fetch("http://localhost:8081/keep")
      .then(res => console.log(new Date().toLocaleString(), res.text()))
  }, 1000)
  ```
- `setTimeout`可保证数据的到达顺序
  ```js
  setTimeout(request, 2000)
  function request () {
    fetch("http://localhost:8081/keep")
    .then(res => {
      console.log(new Date().toLocaleString(), res.text())
      setTimeout(request, 2000)
    })
  }
  ```
### 长轮询(Long Polling / Comet)
- `setInterval`或者`setTimeout`都是通过发多次HTTP请求模拟的,请求过多将加重服务器负担
- 基本思想：
  ```
  服务器检查上次返回的数据与此次请求时的数据之间是否有更新，
  如果有更新则返回新数据并结束此次连接，
  否则服务器 hold 住此次连接(不返回数据,一直到timeout)，直到有新数据时再返回相应。
  而这种长时间的保持连接可以通过设置一个较大的 HTTP timeout 实现
  ```
- 长轮询的两种实现方式
  * 基于Ajax的长轮询方式(虽然下面用的不是Ajax)
    ```js
    const express = require("express")
    const fs = require("fs")
    const path = require("path")
    let app = express()
    let flag = false

    app.listen(3001, () => {

    })
    app.get('/', function (req, res) {
      var html = fs.readFileSync(path.resolve(__dirname, 'user1.html'), 'utf-8');
      res.send(html);
    });	
    app.get("/long", function (req, res) {
      if (!flag) {
        return
      } else {
        res.send("exec.")
      }
    })
    app.get("/ok", function () {
      flag = true
    })
    ```
    ```html
    <!doctype html>
    <html>
    <head></head>
    <body>
        <input type="button" name="button" value="提交" />
      </div>
      <script>
        let btn = document.getElementsByName("button")[0]
        btn.addEventListener("click", () => {
          fetch("http://localhost:3001/ok")
          .then(res => console.log(res.text())) // fetch 会有等待
        })
        function get () {
          console.log(new Date().toLocaleString())
          fetch("http://localhost:3001/long")
          .then((res) => {
            console.log(res)
            if (res.status === 200) {
              res.text().then(data => {
                console.log(data)
              })
            }
          })
        } 
        setInterval(get, 1000)
      </script>
    </body>
    </html>
    ```
  * 基于iframe以及(http streaming)的方式(落时)
    ```
    Iframe是html标记，这个标记的src属性会保持对指定服务器的长连接请求，
    服务器端则可以不停地返回数据，相对于Ajax方式，这种方式跟传统的服务器推则更接近
    这种方式是通过JSONP的方式来响应数据(Ajax方式是通过回调函数)
    ```
## 服务器发送事件(Server-Sent Event)
> - 服务器发送事件（以下简称SSE）是HTML 5规范的一个组成部分，可以实现服务器到客户端的单向数据通信。通过 SSE ，客户端可以自动获取数据更新，而不用重复发送HTTP请求。多用于视频，大文件下载(支持断线重连)。
> - SSE发送的不是一次性的数据包，而是一个数据流(连续不断的发送)  
> - SSE基于http，不像web socket一样是个新协议
> - 一旦连接建立，“事件”便会自动被推送到客户端。服务器端SSE通过 事件流(Event Stream) 的格式产生并推送事件。事件流对应的 MIME类型 为 text/event-stream ，包含四个字段：event、data、id和retry。event表示事件类型，data表示消息内容，id用于设置客户端 EventSource 对象的 last event ID string 内部属性，retry指定了重新连接的时间。  
> - SSE相对于轮询更加简便,更实时，
> - 但SSE只支持`支持服务器到客户端单向的事件推送`,且不支持IE
- 例子(阮一峰)
  ```js
  var http = require("http");
  http.createServer(function (req, res) {
    var fileName = "." + req.url
    if (fileName === "./stream") {
      res.writeHead(200, {
        "Content-Type": "text/event-stream",
        "Cache-Control": "no-cache",
        "Connection": "keep-alive",
        "Access-Control-Allow-Origin": '*',
      });
      res.write("retry: 10000\n");
      res.write("event: connecttime\n");
      res.write("data: " + (new Date()) + "\n\n");
      res.write("data: " + (new Date()) + "\n\n");
      interval = setInterval(function () {
        res.write("data: " + (new Date()) + "\n\n");
      }, 1000);
      req.connection.addListener("close", function () {
        clearInterval(interval);
      }, false);
    }
  }).listen(8844, "127.0.0.1");
  ```
  ```html
  <!DOCTYPE html>
  <html>
  <head>
    <meta charset="utf-8">
  </head>
  <body>
    <div id="example"></div>
    <script>
      var source = new EventSource('http://127.0.0.1:8844/stream')
      var div = document.getElementById('example')
      source.onopen = function (event) {
        div.innerHTML += '<p>Connection open ...</p>'
      }
      source.onerror = function (event) {
        div.innerHTML += '<p>Connection close.</p>'
      }
      source.addEventListener('connecttime', function (event) {
        div.innerHTML += ('<p>Start time: ' + event.data + '</p>')
      }, false)
      source.onmessage = function (event) {
        div.innerHTML += ('<p>Ping: ' + event.data + '</p>')
      }
    </script>
  </body>
  </html>
  ```

## 对比
名称|传统轮询	|长轮询	|服务器发送事件	|WebSocket
-|-|-|-|-
浏览器支持	|几乎所有现代浏览器	|几乎所有现代浏览器	|非IE|主流
服务器负载|与长轮询相似，但长轮询每次发送请求后服务器不需要断开连接			|较少的CPU资源，较多的内存资源和带宽资源	与传统轮询相似，但是占用带宽较少|无需循环等待（长轮询），CPU和内存资源不以客户端数量衡量，而是以客户端事件数衡量。四种方式里性能最佳。
客户端负载	|占用较多的内存资源与请求数。	|与传统轮询相似。	|浏览器中原生实现，占用资源很小。	|同Server-Sent Event。
延迟	|非实时，延迟取决于请求间隔。	|同传统轮询。	|非实时，默认3秒延迟，延迟可自定义。	|实时。
实现复杂度	|非常简单。|	需要服务器配合，客户端实现非常简单。	|需要服务器配合，而客户端实现甚至比前两种更简单。	|需要Socket程序实现和额外端口，客户端实现简单。

## 知识点
- 一个相同的端口可以使用多个不同的`WebSocketServer`(证明?)
- WebSocket的默认端口是：80与443，  
  服务器启用新的端口时，需要修改防火墙规则
- 相同的端口(如:3000)可以被`express`监听,也可以被`WebSocket`监听,  
  * 因为3000端口并非由express监听，而是express调用Node标准的http模块创建的http.Server监听的。  
  * express只是把响应函数注册到该http.Server中了。  
  * 类似的，WebSocketServer也可以把自己的响应函数注册到http.Server中，  
  * 这样，同一个端口，根据协议，可以分别由express和ws处理：
- WebSocket发送的仍然是......(待补充)

## 问题
### 和TCP、HTTP协议的关系
- WebSocket 是基于 TCP 的独立的协议。它与 HTTP 唯一的关系是它的握手是由 HTTP 服务器解释为一个 Upgrade 请求。
- WebSocket协议试图在现有的 HTTP 基础设施上下文中解决现有的双向HTTP技术目标；同样，它被设计工作在HTTP端口80和443，也支持HTTP代理和中间件，
- HTTP服务器需要发送一个“Upgrade”请求，即101 Switching Protocol到HTTP服务器，然后由服务器进行协议转换。

### Sec-WebSocket-Key/Accept 的作用
前面提到了，Sec-WebSocket-Key/Sec-WebSocket-Accept 在主要作用在于提供基础的防护，减少恶意连接、意外连接。  
作用大致归纳如下：
- 避免服务端收到非法的 websocket 连接（比如 http 客户端不小心请求连接 websocket 服务，此时服务端可以直接拒绝连接）
- 确保服务端理解 websocket 连接。因为 ws 握手阶段采用的是 http 协议，因此可能 ws 连接是被一个 http 服务器处理并返回的，此时客户端可以通过 Sec-WebSocket-Key 来确保服务端认识 ws 协议。（并非百分百保险，比如总是存在那么些无聊的 http 服务器，光处理 Sec-WebSocket-Key，但并没有实现 ws 协议。。。）
- 用浏览器里发起 ajax 请求，设置 header 时，Sec-WebSocket-Key 以及其他相关的 header 是被禁止的。这样可以避免客户端发送 ajax 请求时，意外请求协议升级（websocket upgrade）
- 可以防止反向代理（不理解 ws 协议）返回错误的数据。比如反向代理前后收到两次 ws 连接的升级请求，反向代理把第一次请求的返回给 cache 住，然后第二次请求到来时直接把 cache 住的请求给返回（无意义的返回）。
- Sec-WebSocket-Key 主要目的并不是确保数据的安全性，因为 Sec-WebSocket-Key、Sec-WebSocket-Accept 的转换计算公式是公开的，而且非常简单，最主要的作用是预防一些常见的意外情况（非故意的）。

## 参考
- [编写聊天室](https://www.liaoxuefeng.com/wiki/1022910821149312/1103332447876608)
- [WebSocket 详解](https://segmentfault.com/a/1190000012948613)
- [Server-Sent Event 教程](http://www.ruanyifeng.com/blog/2017/05/server-sent_events.html)
- [MDN官方文档](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)