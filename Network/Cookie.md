# Cookie
## Set-Cookie  
属性|说明
-|-
NAME=VALUE | 赋予 Cookie 的名称和其值（必需项）
expires=DATE | Cookie 的有效期（若不明确指定则默认为浏览器关闭前为止）
path=PATH| 将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的文件目录）
domain=域名 | 作为 Cookie 适用对象的域名 （若不指定则默认为创建 Cookie的服务器的域名）
secure | 仅在 HTTPS 安全通信时才会发送 Cookie
HttpOnly | 加以限制，使 Cookie 不能被 JavaScript 脚本访问

## Cookie
- 请求首部字段
- 创建cookie时，可以设置6个部分：name（名称） value（值） expires（过期时间） path（路径） domain（域） secure（安全性） 后4个可选		  										
  * expires 默认是关闭浏览器后过期; 可另外设置过期时间,格式是： .toUTCString() ;
  * 如6个月后过期：`let expire = new Date(); expire.setMonth(expire.getMonth() + 6).toUTCString();`  												
- 浏览器设置`cookie：document.cookie = "UserName=csy;expires=" + expire+";"`													
- cookie不仅特定于某个web域，如`www.aaa.com`，还特定于该域中的具体路径，如：`www.aaa.com/bbb/` ;     
  若 `www.aaa.com/bbb/` 中的页面设置了cookie，那么只有给目录或其子目录中的页面可以读取该cookie	    												
  这是出于安全考虑，避免可以通过 `www.aaa.com/用户/小明` 获取到 `www.aaa.com/用户/小刚` 的cookie	  		  										
  那么实现服务器上 `www.aaa.com/用户/小明/买书` 以及 `www.aaa.com/用户/小明/买游戏` 以及 `www.aaa.com/用户/小明/结账` 3个目录的cookie的互访呢？	  							
- 可以设置第4个属性path： `document.cookie = "UserName=csy;expires=Tue, 28 Dec 2020 00:00:00;path=/用户/小明"`													
```js
function setCookie (name, value, expires, path) {													
  value = escape(value) // cookies 的值中有些字符是不被推荐的, 例如空格,标点, 所以需要转码					
  if (!expires) { // 若 expires 不存在													
    let now = new Date()													
    now.setMonth(now.getMonth() + 6)													
    expires = now.toUTCString()													
  }													
  if (path) { // 若path 存在													
    path = ";Path=" + path													
  }													
  document.cookie = name + "=" + value + ";expires=" + expires + path													
}													
修改cookie： setCookie("Name", "bbb")													
删除cookie，让cookie的有效期为过去时间即可： setCookie("Name", "", "Mon, 1 Jan 1990 00:00:00", "")													
													
cookie示例：  "BAIDUID=7A0F2F82B02CFCEDB1134FB2CC473269:FG=1; BIDUPSID=7A0F2F82B02CFCEDB1134FB2CC473269; PSTM=1539613117; BD_CK_SAM=1; BDORZ=FFFB88E999055A3F8A630C64834BD6D0"													
function getCookie (name) { // 获取Cookie的值, name是字段名													
  let value = document.cookie // 获取页面的cookie													
  let cookieStartsAt = value.indexOf(" " + name + "=") // 一个页面可以有多个cookie, cookie之间默认是以空格间隔													
  													
  if (cookieStartsAt === -1) { // 所查找的cookie可能在第一个或不存在													
    cookieStartsAt = value.indexOf(name + "=") // 再找一次, 这次前面不带空格													
  } 													
  if (cookieStartsAt === -1) { // 所查找的cookie不存在													
    value = null													
  } else {													
    cookieStartsAt = value.indexOf("=", cookieStartsAt) + 1 // 开始取值													
    let cookieEndsAt = value.indexOf(";", cookieStartsAt) 													
    if (cookieEndsAt === -1) { // 没有找到 ";" 说明该cookie在最末尾													
      cookieEndsAt = value.length													
    }													
    value = unescape(value.substring(cookieStartsAt, cookieEndsAt)) 													
  }													
  return value													
}													
```													

## cookie的局限性：													
- 用户禁用cookie 策略是放置一个测试cookie，之后获取它的值，测试是否禁用cookie													
```js
setCookie("TestCookie", "Yes")													
if (!getCookieValue("TestCookie"))  console.log("该用户已禁用cookie")											
```
- 浏览器会限制 cookie 总数（一般50个），超出会删除；一般页面每个域最多也是20条cookie；每条cookie的携带值一般在4KB以内；													
- 策略：把多条cookie信息，按一定顺序杂糅到一条cookie，如：setCookie("userDetail", "csy;44;10 Jan 2000")													


## `Express`实现Cookie示例
- npm i cookie-parser // 假如已经安装则不需要重复安装(因为是官方内置的)
- ```js
  const cookieParase = require('cookie-parser');
  app.use(cookieParase())	// 解析cookie
 
  app.use('/', function (req,res) {
    console.log(req.headers.cookie); // 打印cookie
    res.cookie('user', 'XiaoMing'); // 设置cookie(即给浏览器的响应首部字段中有 Set-Cookie:user=XiaoMing;)
    res.send('succ')
  })
  ```