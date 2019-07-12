# WebSocket

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

## 知识点
- 一个相同的端口可以使用多个不同的`WebSocketServer`(证明?)
- WebSocket的默认端口是：80与443，  
  服务器启用新的端口时，需要修改防火墙规则
- 相同的端口(如:3000)可以被`express`监听,也可以被`WebSocket`监听,  
  * 因为3000端口并非由express监听，而是express调用Node标准的http模块创建的http.Server监听的。  
  * express只是把响应函数注册到该http.Server中了。  
  * 类似的，WebSocketServer也可以把自己的响应函数注册到http.Server中，  
  * 这样，同一个端口，根据协议，可以分别由express和ws处理：
- WebSocket发送的仍然是

## 参考
- [编写聊天室](https://www.liaoxuefeng.com/wiki/1022910821149312/1103332447876608)