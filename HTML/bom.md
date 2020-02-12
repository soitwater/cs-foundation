# bom

## 结构
- window
  * location
  * history
  * document
    - forms
    - images
    - links
  * navigator
  * screen

## 解释
- window			
```		
即浏览器实例，你写的所有js变量，方法都是window的属性	  				
因此，创建window的属性就是：var age = 21 (必须是var 不能是let) 相关解释		  			
而查询是否含有某变量则是 console.log(window.zzz) // 假如不存在，则输出undefined；假如存在，则输出该值					
```

- history
```js
history（网页的后退/前进键）			
history.length			
history.go(0)	当前页面刷新		
history.go(1)	页面前进，且刷新		
history.forward()	前进一页，但不刷新，原页面内容保留		
history.back()	（同理）		
history.go(-1)	页面后退，且刷新		
```

- location
```js
location （表示当前网页）
location.href = "其它网页" // 将“当前页面”加入历史栈
location.replace( "其它网页" ) // 替换当前网页，重置历史栈
console.log (location.href)
console.log (location.protocol) // 'http:'　 'https:'
console.log (location.pathname) // '/learn/199'
console.log (location.search)
console.log (location.hash)
```

- screen
```js
screen
console.log(screen.colorDepth)
console.log (screen.width)
console.log (screen.height)
```

- navigator
```js
navigator（当前浏览器及操作系统的信息）			
常用于“浏览器嗅探”及“特性检测”，确保代码能在当前浏览器中运行			
// 获取地理位置（但一般不会成功，浏览器对地理位置信息一般都有保护）			
function success (position) {			
  // success 是回调函数			
  console.log (position.coords.latitude)			
  console.log (position.coords.longitude)			
  console.log (position.coords.altitude)			
  console.log (position.coords.speed)			
  alert (position.coords.speed)			
}			
function error (errObj) {			
  alert(errObj.message)			
}			
navigator.geolocation.getCurrentPosition(success, error)			
/* navigator - 检测浏览器的类型 */			
var ua = navigator.userAgent			
var isChrome = ua.indexOf ("Chrome")			
console.log (isChrome)			
```
```js
特性检测优于浏览器嗅探
if (typeof navigator.geolocation != "undefined") {
  // use geolocation
}
浏览器嗅探适用于针对单个浏览器，如针对IE浏览器；
浏览器嗅探使用的是 navigator.userAgent;
var ua = navigator.userAgent
var isChrome = ua.indexOf ("Chrome")
console.log (isChrome)
```

- document
```js
document
document.images[0]  // 获取网页中的第一个<img/>
document.links[0]  // 获取网页的第一个<a/>
```

- ??
```js
href:javascript:;									
表示在执行默认操作前，先执行一段JS代码					
```
