# Ajax
- 诞生：Internet自问世以来，一直使用事务特征的通信模型，即浏览器发出请求，服务器接受请求，再返回新页面
- 缺点：一个web应用程序需要包含多个页面，不连续的页面加载方式使用户体验十分糟糕								
- 改进：Ajax，令web应用更接近桌面应用															

## readyState 的可能取值														
- 0——已创建对象，但未调用open() 方法
- 1——已调用open()方法，但未发送请求
- 2——请求已发送，头部和状态已接受到并可用
- 3——接受到来自服务器的响应															
- 4——接收完请求数据															
															
## 属性说明															
* xhr.readyState：XMLHttpRequest对象的状态，等于4表示数据已经接收完毕。															
* xhr.status：服务器返回的状态码，等于200表示一切正常。															
* xhr.responseText：服务器返回的文本数据															
* xhr.responseXML：服务器返回的XML格式的数据															
* xhr.statusText：服务器返回的状态文本。															
															
															
- 如果需要跨域，应该：
  1. 浏览器支持跨域 
  2. 在服务器的响应头处添加`支持跨域`  
     - 其中服务端添加如下的选项：					
       ```js										
       ('Access-Control-Allow-Origin', '*')  允许跨域访问的域															
       ('Access-Control-Allow-Methods', 'POST, GET, OPTIONS')  允许使用的请求方法		
       ('Access-Control-Max-Age', 1000) 在这个时间范围内，所有同类型的请求都将不再发送预检请求而是直接使用此次返回的头作为判断依据，大幅优化请求次数
       ('Access-Control-Allow-Headers', '*')  允许自定义的头部，以逗号隔开，大小写不敏感	
       ('Content-type', 'application/json')															
       ```															

## 手写Ajax															
```js															
let xhr = new  XMLHttpRequest()															
xhr.timeout = 3000															
xhr.ontimeout = (event) => alert("请求超时!")															
let formData = new FormData()															
formData.append("telephone", "110")															
formData.append("password", "120")															
// 若为"GET", 则发送请求时是不携带数据的, 应该.send(null), 如下															
// xhr.open("GET", "http://localhost:8080/login", true); xhr.send(null);															
xhr.open("POST", "http://localhost:8080/login", true) // true是默认, 表示异步发送请求															
xhr.send(formData)															
// 以下是异步的回调，假如是同步的ajax，那么直接 if (xhr.status === 2000) {} ，即没有回调，没有xhr.readState															
xhr.onreadystatechange = () => {															
  if (xhr.readyState === 4 && xhr.status === 200) {															
    console.log(xhr.responseText)															
  } else {															
    console.log(xhr.statusText)															
  }															
}															
```

## Ajax安全策略		
- 同源策略：相同HTTP协议，主机名，端口号的才是同源															
- 跨域是正常的需求。跨域时，浏览器需发送一个自定义的HTTP标题，称为Origin
  * Origin包含发送请求的页面的协议，域名，端口															
    - 如：`http://www.abc.com/index.html`向服务器`http://www.123.com发送CORS请求时` 
    - 其Origin即为: `http://www.abc.com`(端口因为是默认，所以隐藏了)															
    - 同时，服务器需要标识，允许该Origin源，即: `Access-Control-Allow-Origin: http://www.abc.com` 															
- 缺点
  * 无法改变当前url（不支持直接通过网址访问系统某一模块）
  * 不支持浏览器的前进or后退按钮						
  * 例如一个在线阅读小说的web应用，当使用AJAX时数据加载很快，但若用户一不小心刷新页面，那页码又会回到起始页，因为AJAX不能在浏览器的历史会话中保持记录		
- 解决方法：HTML5 的`window.history`															
  * `[data] `传递数据, 不能过大; 
  * `[title]`几乎所有浏览器都忽略该参数(即你传入什么参数都无所谓，不影响该方法执行); 
  * `[url]`需要替换的url，需要同源															
  * `history.replaceState([data], [title], [url])` 跳转到指定url ，会改变浏览器url (不支持浏览器的回退，前进)															
  * `history.pushState([data], [title], [url])` 跳转到指定url ，会改变浏览器url (支持浏览器的回退，前进)															
  * 应用场景
    - 当用户选择一个图片时，图片下方显示对应的文字描述，同时给该图片一个被选中的效果。
    - 当点击后退按钮时，页面应切换到上一个被选中的图片状态，同时图片下方的文字也要一并切换。
    - 当点击前进按钮时也一样。															

## 新版本的XMLHttpRequest
### Level2的改进
```js
// 可以设置HTTP请求的时限。
xhr.timeout = 3000
xhr.ontimeout = function(event) {
  console.log('xxx')
}

// 可以使用FormData对象管理表单数据。
// 可以上传文件。
let formData = new FormData()
formData.append('username', '张三')
xhr.send(formData)

// 可以请求不同域名下的数据（跨域请求）。
xhr.open('GET', 'http://other.server/and/path/to/script')
```
更重要的新特性: 
```js
// 可以获取服务器端的二进制数据。最早的ajax只能接受文本数据,所以将二进制文件当作文本进行传输,然后再在浏览器处理这些二进制文本数据
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png');
xhr.responseType = 'blob' // 还可以设置为其他,表示浏览器返回的数据类型,一般还有 arraybuffer
var blob = new Blob([xhr.response], {type: 'image/png'}) // 接受数据时,用浏览器自带的blob对象

// 可以获得数据传输的进度信息。
// 新版的 XMLHttpRequest 对象,在传送数据时,有一个 progress 事件,用于返回进度信息
xhr.onprogress = updateProgress
xhr.upload.onprogress = updateProgress
function updateProgress(event) {
  if (event.lengthComputable) {
    var percentComplete = event.loaded / event.total;
  }
}
```
## 参考
- [参考链接](http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html)