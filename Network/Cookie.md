# Cookie
## Set-Cookie
响应首部字段，其字段属性有：
属性|说明
-|-
NAME=VALUE | 赋予 Cookie 的名称和其值（必需项）
expires=DATE | Cookie 的有效期（若不明确指定则默认为浏览器关闭前为止）
path=PATH| 将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的文件目录）
domain=域名 | 作为 Cookie 适用对象的域名 （若不指定则默认为创建 Cookie的服务器的域名）
secure | 仅在 HTTPS 安全通信时才会发送 Cookie
HttpOnly | 加以限制，使 Cookie 不能被 JavaScript 脚本访问

## Cookie
请求首部字段
