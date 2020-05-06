# get与post的区别
- 没区别, 都是HTTP协议的方法, HTTP协议的下一层是TCP协议, 其实 get 与 post 的本质都是 TCP链接
- 真正体现区别是因为具体浏览器对get 与 post 的实现方式存在差异
- 例如一般的浏览器：GET产生一个TCP数据包，而POST产生两个TCP数据包
  * GET的请求，浏览器会把http header和data一并发送出去，服务器返回200（返回数据）
  * POST的请求，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200（返回数据）
- 但Firefox的post请求就只发一次

### 是否有缓存
- GET一般是拿数据到本地，通常会缓存
- POST是修改服务端的数据，一般不缓存
### 是否安全
- GET的参数放在url，POST的参数在请求体
### 长度限制
- GET根据浏览器不同实现，其URL长度也不同，一般7K多个字符
- POST发送的请求体大小无限制

## 参考
- [HTTP中GET与POST的区别 99%的错误认识](https://segmentfault.com/a/1190000010872233)
