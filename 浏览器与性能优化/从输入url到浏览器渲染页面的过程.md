# 从输入url到浏览器渲染页面的过程

- 加载资源的形式
  1. 输入url（或跳转页面）加载html
  2. 加载html中的静态资源 eg:  `<script src = '/js/jquery.js'></script>`

- 加载资源的过程
  1. 浏览器根据DNS服务器将域名转化为IP地址
  2. 浏览器向IP地址发送http请求
  3. 服务器收到、处理并返回http请求
  4. 浏览器得到返回内容

- 浏览器渲染页面的过程
  1. 根据HTML结构生成DOM Tree
  2. 根据CSS生成CSSOM
  3. 将DOM和CSSOM整合成RenderTree
  4. 根据RenderTree开始渲染和展示
  5. 遇到`<script>`时，会执行并阻塞渲染（因为`<script>`可以改变DOM的结构与内容）

## 从输入网址到打开网页？
1. `http://www.baidu.com/sample.html` (浏览器用url说：给我sample.html) 生成HTTP请求
   * 解析URL; 
   * 向DNS服务器查询web服务器的IP地址：这一步由全世界的DNS服务器接力完成
   * 根据URL生成请求；	
   * 委托协议栈发送信息	
2. 协议栈：一款软件，把信件放入信封，在信封上写下目的地信息
   网卡：硬件，把信封变成电信号，通过网络发送出去
3. 集线器，交换机：个人电脑在局域网中借集线器，交换机到达路由器
   路由器：互联网的入口，网络运营商会将信封送到目的地
4. 接入网：光纤，电话线等；
   网络运营商：离你家最近的邮局，它与世界各地的网络运营商间以互联网互联
5. 防火墙，缓存服务器：信封到达目标WEB服务器，遇到保安——防火墙
   之后信封中可被重新利用部分被保存在缓存服务器
   （可能会遇到负载均衡器——将信封发到多个目的地）
   （可能会应用互联网中的其他缓存服务）
6. WEB服务器：通过“协议栈”解包，将响应消息装入信封，发回邮箱


## 参考
- [前端JS基础十一(页面加载、性能优化和安全性)](https://www.jianshu.com/p/9210f0f84216)