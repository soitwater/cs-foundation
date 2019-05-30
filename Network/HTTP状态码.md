# HTTP状态码
- 状态码类别
  状态码|类别|原因短语
  -|-|-
  1XX|信息性状态码|接收的请求正在处理
  2XX|成功性状态码|请求正常处理完毕
  3XX|重定向状态码|需要进行附加操作以完成请求
  4XX|客户端错误状态码|服务器无法处理请求
  5XX|服务器错误状态码|服务器处理请求出错
- 常用状态码
  类别|状态码|短语|含义
  -|-|-|-
  2XX|200|OK|请求已被正常处理
  2XX|204|No Content|限制GET，请求处理成功，但无资源可返回
  2XX|206|Partial Content|请求处理成功，响应报文中返回Content-Range指定范围的实体内容
  3XX|301|Moved Permanently|永久性重定向，所请求的资源已分配新URI，以后请求资源应使用新URI
  3XX|302|Found|临时性重定向，所请求资源已被分配至新URI，希望用户本次可使用新URI访问
  3XX|303|See Other|限制GET，与302含义类似
  3XX|304|Not Modified|客户端发送附带条件的请求(首部字段有If-Match If-Modified-Since等),服务器中存在资源但未满足所附带条件(实际与重定向无关) 
  3XX|307|Temporary Redirect|临时重定向,与302含义一致,但不会把POST改为GET
  4XX|400|Bad Request|请求报文存在语法错误
  4XX|401|Unauthorized|请求需要通过HTTP认证(BASIC认证,DIGEST认证)或表示用户认证失败
  4XX|403|Forbidden|用户无该资源访问权限
  4XX|404|Not Found|服务器上不存在该资源
  5XX|500|Internal Server Error|服务器执行请求时故障
  5XX|503|Service Unavailable|服务器超负载或停机维护


