# Github的OAuth

- Authorization 授权
- 登录方式
  * 账号密码
  * 邮箱接受登录链接
  * 手机号接收验证码
- 授权方式
  * Authorization Code
  * Refresh Token
  * Device Code
  * 等等

## OAuth授权流程
- 客户端网页点击按钮，跳转至Github Oauth
- Github确认授权，跳转并携带`code`回客户端页面
- 客户端页面请求服务器
- 服务器向Github请求`access_token`
- Github返回`access_token`给服务端
- 服务端将`access_token`写入`session`
- 服务端返回客户端请求，`access_token`被写入`cookie`

## 注册Github-OAuth-App
- 登录Github —— 头像 —— Settings —— Developer settings —— OAuth App
- 需要填写OAuth信息
  * 其中`Homepage URL`，随便填：`http://localhost:3000`
  * 其中`Authorization callback URL`,表示`OAuth`返回的链接地址,例如`http://localhost:3000/auth`(域名+路由)

## OAuth字段(写在url)
- 获取`code`
  * client_id
  * scope(`授权范围`)
- 获取`token`
  * client_id, client_secret
  * code

## OAuth安全策略
- 一次性`code`
- client_id，client_secret
- `Authorization callback URL`(即登录后的`重定向uri`)

## 参考
- [官方文档](https://developer.github.com/apps/building-oauth-apps/authorizing-oauth-apps/)