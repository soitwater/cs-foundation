# HTTP
- 请求报文的组成：请求方法，请求URI，协议版本，可选的请求首部字段，内容实体  
  例：
  ```http
  POST /form/entry HTTP/1.1

  Host: hackr.jp
  Connection: keep-alive
  Content-Type: application/x-www-form-urlencoded
  Content-Length: 16
  
  name=a&age=14
  ```
- 响应的组成   
  ```
  协议版本 状态码 解释状态码的短语  
  可选的响应首部字段  
  资源实体的主体  
  ```
  例：
  ```http
  HTTP/1.1 200 OK
  Date: Wed May 29 2019 09:35:09 GMT
  Content-Legth: 362
  Content-Type: text/html

  <html>
  </html>
  ```
- HTTP不保存状态，即无状态协议（因请求之间是独立的，所以无法记住用户的历史操作，登录状态等）  
  可以使用Cookie保存状态（通过请求/响应报文中加入Cookie来控制客户端的状态）
- HTTP方法
  * `GET`
  * `POST`：传输信息
  * PUT：传输文件，但存在安全性问题，一般不采用
  * DELETE：删除文件，但存在安全性问题，一般不采用
  * HEAD：获得报文头部
  * OPTIONS：询问支持的方法
    - `OPTIONS`应用场景
      * 获取服务器支持的HTTP请求方法；也是黑客经常使用的方法。
      * 用来检查服务器的性能。例如：AJAX进行跨域请求时的预检，需要向另外一个域名的资源发送一个HTTP OPTIONS请求头，用以判断实际发送的请求是否安全。
  * TRACE：追踪路径，很少用，容易引发跨站追踪攻击
  * CONNECT：要求用隧道协议连接代理，常用于加密
- 建立持久连接节省通信量  
  * 若某HTTP页面包含大量的图片，则该页面加载时会多次建立TCP连接，因此需要 HTTP keep-alive  
  * HTTP keep-alive 除非客户端/服务端明确提出断开连接，否则保持TCP连接状态
  * HTTP/1.1默认持久连接
  * （先建立TCP连接后，再进行HTTP请求/响应的）
- 管线化
  以前必须等待上一请求获取响应后再发出下一请求，现在默认可以并行发出多个请求
- HTTP报文的通用格式
  ```
  请求行/状态行
  各种首部字段
  空行/
  报文主体
  ```
- 报文主体与实体主体
  通常报文 === 实体，但有时为了提高HTTP传输效率，会对所传输的数据进行编码，此时报文为编码前数据，实体为编码后数据
- 常用的压缩编码方式
  * gzip
- 分块传输编码  
  在HTTP中，若实体资源未加载完成，浏览器无法显示所请求页面。  
  所以在传输大量数据时，可以把数据切分成多块，让浏览器逐步显示。
- 发送多种数据的多部分对象集合  
  在HTTP传输中，报文内支持含多种类型的数据，但需要在首部字段添加`Content-type`
  * multipart/form-data
  * multipart/byteranges
  * 补充......
- 获取部分内容的范围请求  
  例如下载大文件时，即使中间因为网络中断，也无需重头开始下载    
  需要使用`Content-Range`首部字段
- 内容协商返回最合适的内容  
  例如根据浏览器默认语言返回中文网页或英文网页  
  内容协商方式有2种：根据首部字段或用户自主选择：
  * Accept
  * Accept-Charset
  * Accept-Encoding
  * Accept-Language
  * Content-Language

## HTTP请求方法
- 8个方法

### GET  
- 获取资源。例如：
```js
请求|GET /index.html HTTP/1.1   
    |Host: www.hackr.jp 
    |If-Modified-Since: Thu, 12 Jul 2012 07:30:00 GMT
————————————————————————————————————————————————————————————
响应|仅返回2012年7 月12日7 点30分`以后`更新过的index.html页面资源。
    |如果未 有内容更新，则以状态码`304 Not Modified`作为响应返回
```

### POST  
- 传输实体主体  
- 与`GET`方法类似，但POST的主要目的并不是获取响应的主体内容

### PUT
- 传输文件，并保存到请求URI指定的位置  
- 但因为HTTP/1.1的PUT方法无验证机制,任何人均可上传文件,因此存在安全性问题

### DELETE
- 用于删除文件  
- 与`PUT`方法相反，用于按请求URI删除指定的资源  
- 与`PUT`一样无验证机制，存在安全性问题

### HEAD
- 获取报文首部  
- 与`GET`类似，只是不返回报文主体,多用于确认URI的有效性及资源的更新日期

### OPTIONS
- 询问(请求URI)对应的资源，它所支持的方法

### TRACE
- 追踪路径(依赖首部字段`Max-Forwards`)  
- 请求想要连接目标服务器可能需要代理中转,`Trace`方法可以查询发出去的请求是怎样被加工/篡改的  
- 少用,因为容易引发跨站追踪

### CONNECT
- 发出请求：要求用隧道协议(SSL/TLS)连接代理,收到响应后即进入网络隧道    
- 报文格式:
```
CONNECT: proxy.abc.com:80 HTTP/1.1
首部字段
```

## 参考
- [图解HTTP/P38]()