# CSRF

## 概念
跨站点请求伪造。CSRF攻击者在用户已经登录目标网站之后，诱使用户访问一个攻击页面，利用目标网站对用户的信任，以用户身份在攻击页面对目标网站发起伪造用户操作的请求，达到攻击目的。

## 原理
CSRF攻击是源于WEB的隐式身份验证机制！WEB的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的

## 防御
- 添加验证码，保证当前操作是用户所为
- Token
  * 保证没有 XSS 漏洞
  * 用户访问某个页面时，服务端生成一个Token，放在用户的Session中，或者浏览器的Cookie中。
  * 用户提交请求时，需要附带 Token，服务端验证请求中的Token是否与用户Session（或Cookies）中的Token一致，一致为合法请求，不是则非法请求。
  * Token 必须随机，不可预测，及其保密，无法被恶意获取。由于Token的存在，攻击者无法构造含合法Token的CSRF攻击。

## 解释
CSRF 是借用了当前操作者的权限来偷偷地完成某个操作，而不是拿到用户的信息。

例如，一个支付类网站，给他人转账的接口是`http://buy.com/pay?touid=999&money=100`，而这个接口在使用时没有任何密码或者 token 的验证，只要打开访问就直接给他人转账。一个用户已经登录了`http://buy.com`，在选择商品时，突然收到一封邮件，而这封邮件正文有这么一行代码`<img src="http://buy.com/pay?touid=999&money=100"/>`，他访问了邮件之后，其实就已经完成了购买。

CSRF 的发生其实是借助了一个 cookie 的特性。我们知道，登录了`http://buy.com`之后，cookie 就会有登录过的标记了，此时请求`http://buy.com/pay?touid=999&money=100`是会带着 cookie 的，因此 server 端就知道已经登录了。而如果在`http://buy.com`去请求其他域名的 API 例如`http://abc.com/api`时，是不会带 cookie 的，这是浏览器的同源策略的限制。但是 —— **此时在其他域名的页面中，请求`http://buy.com/pay?touid=999&money=100`，会带着`buy.com`的 cookie ，这是发生 CSRF 攻击的理论基础。**

## 参考
- [Web安全之CSRF攻击](https://www.cnblogs.com/lovesong/p/5233195.html)
- [浅谈CSRF攻击方式](https://www.cnblogs.com/wangyuyu/p/3388169.html)