# defer与async

- defer：在DOM解析完成之后且所有JS下载完成之后，解析JS（JS会先下载，但不解析），一般是按顺序解析（具体看浏览器），  
  用法：添加 defer（仅适用外部js文件，`<script>`标签内的JS文件无效）
- async: 不管DOM，也不必等待所有的JS加载完，只有自己加载完成，就立即解析JS

## 渲染过程
### defer
```html
<script src="a.js" defer></script>
<script src="b.js" defer></script>
```
1. 浏览器开始解析HTML网页 
2. 解析过程中，发现带有defer属性的script标签 
3. 浏览器继续往下解析HTML网页，同时并行下载script标签中的外部脚本 
4. 浏览器完成解析HTML网页，此时再执行下载的脚本
* 只有等到DOM加载完成后，才会执行a.js和b.js

### async
```html
<script src="a.js" async></script>
<script src="b.js" async></script>
```
1. 浏览器开始解析HTML网页 
2. 解析过程中，发现带有async属性的script标签 
3. 浏览器继续往下解析HTML网页，同时并行下载script标签中的外部脚本 
4. 脚本下载完成，浏览器暂停解析HTML网页，开始执行下载的脚本 
5. 脚本执行完毕，浏览器恢复解析HTML网页
* async属性可以保证脚本下载的同时，浏览器继续渲染。但一旦采用这个属性，就无法保证脚本的执行顺序。哪个脚本先下载结束，就先执行那个脚本。


## 参考
- [浅谈script标签中的async和defer](http://www.cnblogs.com/jiasm/p/7683930.html)
- [解决阻塞效应之defer和async](https://blog.csdn.net/hbts_901111zb/article/details/78963956)