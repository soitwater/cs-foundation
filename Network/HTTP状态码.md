# HTTP状态码
## 状态码类别
  | 状态码 | 类别             | 原因短语                   |
  | ------ | ---------------- | -------------------------- |
  | 1XX    | 信息性状态码     | 接收的请求正在处理         |
  | 2XX    | 成功性状态码     | 请求正常处理完毕           |
  | 3XX    | 重定向状态码     | 需要进行附加操作以完成请求 |
  | 4XX    | 客户端错误状态码 | 服务器无法处理请求         |
  | 5XX    | 服务器错误状态码 | 服务器处理请求出错         |
## 常用状态码
  | 类别 | 状态码 | 短语                          | 含义                                                                                                                  |
  | ---- | ------ | ----------------------------- | --------------------------------------------------------------------------------------------------------------------- |
  | 1XX  | 100    | Continue                      | 收到了请求的起始部分，客户端应该继续请求                                                                              |
  | 1XX  | 101    | Switching Protocols           | 服务器正根据客户端的指示将协议切换成Update Header列出的协议                                                           |
  | 2XX  | 200    | OK                            | 请求已被正常处理                                                                                                      |
  | 2XX  | 201    | Created                       | 对于那些要服务器创建对象的请求来说，资源已创建完                                                                      |
  | 2XX  | 202    | Accepted                      | 请求已接受， 但服务器尚未处理                                                                                         |
  | 2XX  | 203    | Non-Authoritative Information | 服务器已将事务成功处理，只是实体Header包含的信息不是来自原始服务器，而是来自资源的副本                                |
  | 2XX  | 204    | No Content                    | 限制GET，请求处理成功，但无资源可返回                                                                                 |
  | 2XX  | 205    | Reset Content                 | 浏览器应该重置当前页面上所有的HTML表单                                                                                |
  | 2XX  | 206    | Partial Content               | 请求处理成功，响应报文中返回Content-Range指定范围的实体内容                                                           |
  | 3XX  | 300    | Multiple Choices              | 客户端请求了实际指向多个资源的URL.300是和一个选项列表一起返回的，然后用户就可以选择他希望的选项了                     |
  | 3XX  | 301    | Moved Permanently             | 永久性重定向，所请求的资源已分配新URI，以后请求资源应使用新URI                                                        |
  | 3XX  | 302    | Found                         | 临时性重定向，所请求资源已被分配至新URI，希望用户本次可使用新URI访问                                                  |
  | 3XX  | 303    | See Other                     | 限制GET，与302含义类似                                                                                                |
  | 3XX  | 304    | Not Modified                  | 客户端发送附带条件的请求(首部字段有If-Match If-Modified-Since等),服务器中存在资源但未满足所附带条件(实际与重定向无关) |
  | 3XX  | 305    | Use Proxy                     | 必须通过代理访问资源                                                                                                  |
  | 3XX  | 306    | 未使用                        | 此状态码未使用                                                                                                        |
  | 3XX  | 307    | Temporary Redirect            | 临时重定向,与302含义一致,但不会把POST改为GET                                                                          |
  | 4XX  | 400    | Bad Request                   | 请求报文存在语法错误                                                                                                  |
  | 4XX  | 401    | Unauthorized                  | 请求需要通过HTTP认证(BASIC认证,DIGEST认证)或表示用户认证失败                                                          |
  | 4XX  | 402    | Payment Required              | 要求付款，这个状态码还没被使用                                                                                        |
  | 4XX  | 403    | Forbidden                     | 用户无该资源访问权限                                                                                                  |
  | 4XX  | 404    | Not Found                     | 服务器上不存在该资源                                                                                                  |
  | 4XX  | 405    | Method Not Allowed            | 不支持该Request的方法                                                                                                 |
  | 4XX  | 406    | Not Acceptable                | 此状态码未使用                                                                                                        |
  | 4XX  | 407    | Proxy Authentication Required | 同401，要求进行代理认证                                                                                               |
  | 4XX  | 408    | Request Timeout               | 如果客户端完成请求时花费的时间太长， 服务器可以回送这个状态码并关闭连接                                               |
  | 4XX  | 409    | Conflict                      | 发出的请求在资源上造成了一些冲突                                                                                      |
  | 5XX  | 500    | Internal Server Error         | 服务器执行请求时故障                                                                                                  |
  | 5XX  | 502    | Bad Gateway                   | 代理使用的服务器遇到了上游的无效响应                                                                                  |
  | 5XX  | 503    | Service Unavailable           | 服务器超负载或停机维护                                                                                                |
  | 5XX  | 504    | Gateway Timeout               | 与状态吗408类似， 但是响应来自网关或代理，此网关或代理在等待另一台服务器的响应时出现了超时                            |


## 参考
- [状态码](https://blog.csdn.net/hzw05103020/article/details/47276005)