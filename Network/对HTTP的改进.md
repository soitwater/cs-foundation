# 对HTTP的改进

## SPDY
当页面部分更新时，若重新加载页面显得不必要，因此有了Ajax技术  
但Ajax仍不够，还需要协议层面的改进，让HTTP加载资源更快。  
因此Google又研究了SPDY  
- SPDY没有改写HTTP协议，而是在TCP/IP的应用层与传输层之间以会话层形式运作，且表示层使用SSL
- SPDY的特点
  * 所有的HTTP请求都在一条TCP连接上完成，效率高
  * 可并发处理请求，请求间可设置优先级
  * 可压缩请求/响应首部字段
  * 即使没有请求，服务器也可以向客户端发送数据
## WebSocket
SPDY仍是HTTP协议的改进，而WebSocket则是独立的新协议  
WebSocket特点
- WebSocket连接需要由客户端建立，一旦通信连接，任意一方都可以向对方发送报文
- WebSocket连接一旦建立便一直保持连接状态
- 首部字段更精简
## HTTP/2.0
微软起草，建立在谷歌的SPDY以及WebSocket基础之上