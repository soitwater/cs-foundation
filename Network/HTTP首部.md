# HTTP首部
- 首部字段是为了给客户端和服务端提供报文主体大小、所使用的语言、认证信息等内容  
- 首部字段若存在多个值，值之间使用 `, ` 隔开
- 首部字段若出项2次或以上，不同浏览器处理方式不同，可能优先第一个，也可能优先最后一个

# 首部字段类型
- 通用首部字段  
  请求报文和响应报文双方都会使用的首部
- 请求首部字段  
  发送请求报文时使用的首部
- 响应首部字段  
  返回响应报文时使用的首部
- 实体首部字段  
  针对请求报文和响应报文的实体部分(附带文件数据)使用的首部

# 首部字段
## 通用首部字段
  首部字段名|说明
  -|-
  Cache-Control | 控制缓存的行为
  Connection | 逐跳首部、连接的管理
  Date | 创建报文的日期时间
  Pragma | 报文指令
  Trailer | 报文末端的首部一览，多用于分块传输编码
  Transfer-Encoding | 指定报文主体的传输编码方式，仅对分块传输编码有效
  Upgrade | 升级为其他协议
  Via | 代理服务器的相关信息
  Warning | 错误通知，返回警告码，警告内容等
## 请求首部字段
  首部字段名 | 说明
  -|-
  Accept | 用户代理可处理的媒体类型
  Accept-Charset | 优先的字符集
  Accept-Encoding | 优先的内容编码，如gzip
  Accept-Language | 优先的语言（自然语言）
  Authorization | Web认证信息
  Expect | 期待服务器的特定行为
  From | 用户的电子邮箱地址
  Host | 请求资源所在服务器,多用于区分单服务器分配多域名,`请求首部字段中唯一必须声明字段`
  If-Match | 比较实体标记（ETag）
  If-Modified-Since | 比较资源的更新时间
  If-None-Match | 比较实体标记（与 If-Match 相反）
  If-Range | 资源未更新时发送实体 Byte 的范围请求
  If-Unmodified-Since | 比较资源的更新时间（与If-Modified-Since相反）
  Max-Forwards | 最大传输逐跳数
  Proxy-Authorization | 代理服务器要求客户端的认证信息
  Range | 实体的字节范围请求
  Referer | 对请求中 URI 的原始获取方
  TE | 传输编码的优先级
  User-Agent | HTTP 客户端程序的信息
## 响应首部字段
  首部字段名 | 说明
  -|-
  Accept-Ranges | 是否接受字节范围请求
  Age | 推算资源创建经过时间
  ETag | 资源的匹配信息
  Location | 令客户端重定向至指定URI
  Proxy-Authenticate | 代理服务器对客户端的认证信息
  Retry-After | 对再次发起请求的时机要求
  Server | HTTP服务器的安装信息
  Vary | 代理服务器缓存的管理信息
  WWW-Authenticate | 服务器对客户端的认证信息
## 实体首部字段
  首部字段名 | 说明
  -|-
  Allow | 资源可支持的HTTP方法
  Content-Encoding | 实体主体适用的编码方式
  Content-Language | 实体主体的自然语言
  Content-Length | 实体主体的大小（单位：字节）
  Content-Location | 替代对应资源的URI
  Content-MD5 | 实体主体的报文摘要
  Content-Range | 实体主体的位置范围
  Content-Type | 实体主体的媒体类型
  Expires | 实体主体过期的日期时间
  Last-Modified | 资源的最后修改日期时间
## 非HTTP/1.1首部字段
  * Cookie：请求首部字段
  * Set-Cookie：响应首部字段
  * Content-Disposition

## 非标准的由浏览器提供的首部字段
- DNT: 0/1  
  是否同意被精准广告追踪
- P3P
  用于保护用户隐私

# End-to-end、Hop-by-hop
从客户端发送请求到服务端，中间后经过许多中转站，逐跳首部会在遇到缓存/代理服务器后失效，端到端首部则不会  
- End-to-end 端到端头部 
  * 此类头部字段会转发给 请求/响应 的最终接收目标。 
  * 必须保存在由缓存生成的响应头部。 
  * 必须被转发。
- Hop-by-hop 逐跳首部 
  * 此类头部字段只对单次转发有效。会因为转发给缓存/代理服务器而失效。 
  * HTTP 1.1 版本之后，如果要使用Hop-by-hop头部字段则需要提供Connection字段。 
- 除了一下8个字段为逐跳字段，其余均为端到端字段。 
  * Connection
  * Keep-Alive
  * Proxy-Authenticate
  * Proxy-Authenrization
  * Trailer
  * TE
  * Tranfer-Encoding
  * Upgrade

# 部分首部字段解析
## Cache-Control
### 缓存请求指令
指令 | 参数 | 说明
-|-|-
no-cache | 无 | 强制向源服务器再次验证
no-store | 无 | 不缓存请求或响应的任何内容
max-age = [ 秒] | 必需 | 响应的最大Age值
max-stale( = [ 秒]) | 可省略 | 接收已过期的响应
min-fresh = [ 秒] | 必需 | 期望在指定时间内的响应仍有效
no-transform | 无 | 代理不可更改媒体类型
only-if-cached | 无 | 从缓存获取资源
cache-extension | - | 新指令标记（token）
### 缓存响应指令
指令|参数|说明
-|-|-
public | 无 | 可向任意方提供响应的缓存，该缓存所有用户都可利用
private | 可省略 | 仅向特定用户返回响应，该缓存仅特定用户可利用
no-cache | 可省略 | 缓存前必须先确认其有效性，即判断资源是否过期再决定是否用缓存
no-store | 无 | 不缓存请求或响应的任何内容，即不必判断直接不用缓存的资源
no-transform | 无 | 代理不可更改媒体类型
must-revalidate | 无 | 可缓存但必须再向源服务器进行确认
proxy-revalidate | 无 | 要求中间缓存服务器对缓存的响应有效性再进行确认
max-age = [ 秒] | 必需 | 响应的最大Age值，即资源保存为缓存的最长时间
s-maxage = [ 秒] | 必需 | 公共缓存服务器响应的最大Age值，若仅向单一用户重复返回响应则该指令无效
cache-extension | - | 新指令标记（token），即可以自定义指令，若缓存服务器无法理解将会忽略该指令值
## Connection
  * Connection: 不再转发的首部字段名（即 hop-by-hop）
  * Connection: close/Keep-Alive 管理持久连接
## Accept  
  值可以是：
  * 文本文件  
  text/html, text/plain, text/css ...  
  application/xhtml+xml, application/xml ...
  * 图片文件  
  image/jpeg, image/gif, image/png ...
  * 视频文件  
  video/mpeg, video/quicktime ...
  * 应用程序使用的二进制文件  
  application/octet-stream, application/zip

## 小结
常用的请求头和响应头
```
1)请求(客户端->服务端[request]) 
    GET(请求的方式) /newcoder/hello.html(请求的目标资源) HTTP/1.1(请求采用的协议和版本号) 
    Accept: */*(客户端能接收的资源类型) 
    Accept-Language: en-us(客户端接收的语言类型) 
    Connection: Keep-Alive(维护客户端和服务端的连接关系) 
    Host: localhost:8080(连接的目标主机和端口号) 
    Referer: http://localhost/links.asp(告诉服务器我来自于哪里) 
    User-Agent: Mozilla/4.0(客户端版本号的名字) 
    Accept-Encoding: gzip, deflate(客户端能接收的压缩数据的类型) 
    If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT(缓存时间)  
    Cookie(客户端暂存服务端的信息) 
    Date: Tue, 11 Jul 2000 18:23:51 GMT(客户端请求服务端的时间)
```
```js
2)响应(服务端->客户端[response])
    HTTP/1.1(响应采用的协议和版本号) 200(状态码) OK(描述信息)
    Location: http://www.baidu.com(服务端需要客户端访问的页面路径) 
    Server:apache tomcat(服务端的Web服务端名)
    Content-Encoding: gzip(服务端能够发送压缩编码类型) 
    Content-Length: 80(服务端发送的压缩数据的长度) 
    Content-Language: zh-cn(服务端发送的语言类型) 
    Content-Type: text/html; charset=GB2312(服务端发送的类型及采用的编码方式)
    Last-Modified: Tue, 11 Jul 2000 18:23:51 GMT(服务端对该资源最后修改的时间)
    Refresh: 1;url=http://www.it315.org(服务端要求客户端1秒钟后，刷新，然后访问指定的页面路径)
    Content-Disposition: attachment; filename=aaa.zip(服务端要求客户端以下载文件的方式打开该文件)
    Transfer-Encoding: chunked(分块传递数据到客户端）  
    Set-Cookie:SS=Q0=5Lb_nQ; path=/search(服务端发送到客户端的暂存数据)
    Expires: -1//3种(服务端禁止客户端缓存页面数据)
    Cache-Control: no-cache(服务端禁止客户端缓存页面数据)  
    Pragma: no-cache(服务端禁止客户端缓存页面数据)   
    Connection: close(1.0)/(1.1)Keep-Alive(维护客户端和服务端的连接关系)  

    Date: Tue, 11 Jul 2000 18:23:51 GMT(服务端响应客户端的时间)
    在服务器响应客户端的时候，带上Access-Control-Allow-Origin头信息，解决跨域的一种方法。
```