# get与post的区别
- 没区别, 都是HTTP协议的方法, HTTP协议的下一层是TCP协议, 其实 get 与 post 的本质都是 TCP链接
- 真正体现区别是因为具体浏览器对get 与 post 的实现方式存在差异
- 例如一般的浏览器：GET产生一个TCP数据包，而POST产生两个TCP数据包
  * GET的请求，浏览器会把http header和data一并发送出去，服务器返回200（返回数据）
  * POST的请求，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200（返回数据）
- 但Firefox的post请求就只发一次

## 参考
- [HTTP中GET与POST的区别 99%的错误认识](https://segmentfault.com/a/1190000010872233)
