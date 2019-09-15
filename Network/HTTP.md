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