# XSS
## 概念
跨站脚本攻击，攻击者在 WEB 页面中插入恶意的 HTML 代码，当用户浏览该页面时，嵌入其中的 恶意 HTML 代码会被执行。

## 攻击手段（Ddos/Cookie/JS,CSS/流量劫持/iframe）
- 盗窃 Cookie（document.cookie）
- 破坏正常页面结构，插入恶意内容(通过 JS 或 CSS)
- 实现 Ddos 攻击效果（XSS最强大的攻击手段之一）（通过合理的客户端请求大量占用服务端资源;通过携带过程cookie信息使服务端返回400状态码）
- 流量劫持（通过 window.location.href 跳转到其他页面）
- 利用 iframe、XMLHttpRequest 等，以被攻击者身份执行一些操作（发微博，加好友，发起 CSRF 攻击）

## 攻击分类
- 反射型
  * XSS 代码出现在 URL 中，作为输入提交到服务器上。服务器解析后响应，XSS 随响应内容返回浏览器，最后浏览器执行 XSS 代码
    - 植入代码自动触发 ```/?xss=<img src="null" onerror="alert(1)" />```
    - 植入代码引诱触发 ```/?xss=<p onclick="alert('点我')" />点我</p>```
    - 植入代码嵌入页面 ```/?xss=<iframe src="//www.baidu.com/t.html"></iframe>```
- 存储型
  * XSS 代码通过 留言评论，表单提交等，会被存储在服务器(数据库，内存，文件系统)中，下此请求目标页面时不必再次提交 XSS 代码

## 防御
XSS 攻击的关键在输入点(提交XSS代码)以及输出点(执行XSS代码),主要依靠“编码”及“过滤”进行防护
- 对用户输入的数据进行HTML Entity编码
  * 例如在评论区评论: ```<img onerror="dosomething()" src="null"/>```，实际会被后台编码为```&lt;img onerror="dosomething()" src="null"/&gt;```
- 过滤
  * 移除用户上传的DOM属性，如onerror等
  * 移除用户上传的Style节点、Script节点、Iframe节点等
- 其他
  * 对重要的 cookie 设置 httpOnly, 防止客户端通过 document.cookie 读取 cookie，此 HTTP头由服务端设置
  * 将不可信的值输出 URL参数之前，进行 URLEncode操作，而对于从 URL参数中获取值一定要进行格式检测（比如你需要的是URL，就判读是否满足URL格式）。
  * 不要使用 Eval来解析并运行不确定的数据或代码，对于 JSON解析请使用 JSON.parse() 方法




## 参考
- [WEB安全 - 认识与防御XSS攻击](http://www.imooc.com/article/67689)
- [《Web安全-XSS》(知识点小结)](http://www.imooc.com/article/23160)
- [HTML Entity 字符实体(字符转义)](https://www.cnblogs.com/polk6/p/html-entity.html)