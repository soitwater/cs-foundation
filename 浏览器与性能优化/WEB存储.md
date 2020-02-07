# WEB 存储

## Cookie
- 功能：
  * 判断用户是否已经登录/ 保持登录状态
  * 记录历史操作（购物车），已经被 localStorage 取代
- Cookie 是HTTP的扩展，弥补HTTP无状态协议的弊端（用户再一次请求结束后再次向服务器发起请求，服务器并不知道仍是该用户）
  * 有2个HTTP头部专门负责设置以及发送Cookie：Set-Cookie 以及 Cookie
- 机制
  * 客户端发送一个http请求到服务器端 
  * 服务器端发送一个http响应到客户端，其中包含Set-Cookie头部 
  * 客户端发送一个http请求到服务器端，其中包含Cookie头部 
  * 服务器端发送一个http响应到客户端
- 使用
  * 生命周期
    ```js
    Cookie cookie = new Cookie("username","helloweenvsfei"); // 新建Cookie
    cookie.setMaxAge(Integer.MAX_VALUE); // 设置生命周期为MAX_VALUE
    response.addCookie(cookie); // 输出到客户端
    ```
  * 增删改
    ```js
    cookie.setMaxAge(0) // 删, 让cookie立即失效

    ```
  * 安全属性
    ```js
    Cookie cookie = new Cookie("time", "20080808"); // 新建Cookie
    cookie.setSecure(true); // 设置安全属性
    response.addCookie(cookie); // 输出到客户端
    ```
- 案例（永久登录）
  * 密码加密后放入Cookie，下此访问时检查用户名以及密码，与数据库进行比较；
  * 把登录时间的时间戳放入Cookie以及数据库，下此登录时验证登录名以及历史登录时间戳；

## Session
对Cookie的改进，Session是在服务端记录用户的登录状态。
- 机制
  * 用户第一次登录时，在服务端生成一个Session以及唯一的Session ID
  * 在随后的请求中，服务端将Session ID返回并存储在客户端（可以是 Cookie）
  * 以后的客户端请求都会携带Session ID用以检验用户身份

## localStorage 与 SessionStorage
- 过期时间：localStorage 永久存储，永不失效除非手动删除  
           sessionStorage 浏览器重新打开后就消失了
- 大小：每个域名是5M
### API
- getItem //取记录
- setIten//设置记录
- removeItem//移除记录
- key//取key所对应的值
- clear//清除记录

### localStorage 面试题目
- a.meituan.com 和 b.meituan.com 这两个域能够共享同一个 localStorage 吗？  
  `不能`
- 在 webview 中打开一个页面：i.meituan.com/home.html，点击一个按钮，  
  调用 js 桥打开一个新的 webview：i.meituan.com/list.html，  
  这两个分属不同 webview 的页面能共享同一个 localStorage 吗？  
  `能`
- 如果 localStorage 存满了，再往里存东西，或者要存的东西超过了剩余容量，会发生什么？  
  `无法存放并报错`

### localStorage的缺陷
- 在大公司，同一个域名下可能存在几十上百条业务线，每条业务线都可能因为各种理由往 localStorage 里塞东西，跨页面传数据啦、缓存啦、离线化啦、性能优化啦...，5M 看起来很多，其实很快就用完了。
- 解决方案
  * 降级到 sessionStorage, 缺点在于 sessionStorage 强调的是`同一个会话`, 因此`sessionStorage` 无法跨页面共享
  * 降级到 cookie, 缺点是cookie 一共才 50 个，总大小不超过 4k，且将造成请求头过大(容易引发 413 错误)
  * 降级到 url, 缺点同样是长度限制
  * 降级到单页面应用, 缺点是需重写当前业务
  * 依赖后端添加更多接口, 缺点是涉及核心业务流程的改造
  * 互相伤害(把前人存放在本地 localStorage 里的数据都清空), 缺点是在 safari 隐私模式下不支持 localStorage 的存取
  * 架构时，合理规划二级域名，如：`a.qq.com`, `b.qq.com` 
- 问题核心
  * localStorage 归根结底就两个作用：持久化存储与跨页面传数据
  * 持久化存储不会出问题，存不进去就存不进去呗，取不出来就去其它地方取，或者不取
  * 问题就出在跨页面传数据上，上一个页面因为 localStorage 存满导致数据没有写入，下一个页面读取数据为空，从而导致错误
  * 使用`indexedDB`
- 互相伤害
  ```js
  const QUOTA_EXCEEDED_ERR_CODE = 22
  function write (key, data) {
    try {
      localStorage.setItem(key, data);
    } catch (e) {
      if (e.code === QUOTA_EXCEEDED_ERR_CODE) {
        localStorage.clear();
        localStorage.setItem(key, data);
      }
    }
  }
  ```

## indexedDB
略

## 异同
   - Cookie 与 Session
     * cookie数据存放在客户的浏览器上，session数据放在服务器上；
     * cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗，考虑到安全应当使用session；
     * session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能。考虑到减轻服务器性能方面，应当使用COOKIE；
     * 单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的COOKIE不能超过3K

   - sessionStorage 、localStorage 和 cookie
     * 共同点：都是保存在浏览器端，且同源的。
     * 区别：
       1. cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递；cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。存储大小限制也不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。
       2. 而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。
       3. 数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
       4. 作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。Web Storage 支持事件通知机制，可以将数据更新的通知发送给监听者。Web Storage 的 api 接口使用更方便。

## 参考
- [cookie、 sessionStorage 、localStorage之间的区别和使用](http://www.cnblogs.com/caiyezi/p/5619506.html)
- [理解Cookie和Session机制](https://www.cnblogs.com/andy-zhou/p/5360107.html)
- [indexedDB 前端数据库（使用的简单案例）](https://www.cnblogs.com/oukele/p/10727790.html)
- [浏览器数据库 IndexedDB 入门教程](http://www.ruanyifeng.com/blog/2018/07/indexeddb.html)
- [html5本地存储（localStorage）使用介绍](https://www.cnblogs.com/shizhouyu/p/4021717.html)
- [localStorage 存满了怎么办？](https://www.cnblogs.com/kidney/p/9058352.html)