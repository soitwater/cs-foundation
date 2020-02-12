# JavaScript权威指南

## 第八章 到 第十四章
- 形参是函数定义时,暂时用于标识变量值的变量;实参是函数调用时,传入该函数的值
- 'use strict'下全局对象是`undefined`
  ```js
  // 检测是否是严格模式
  'use strict'
  let strict = function() {
    return !this
  }
  let lg = console.log
  lg(strict())
  ```
- 构造函数使用`new Object`与`new Object()`等价
- 垃圾知识：`function`创建的称函数,写在对象里的函数称方法
- 因为js的形参都是可选的,因此往往需要判断该形参是否传入
  ```js
  // 有方法 function fn (a) {}
  // 判断是否有参数 a
  a = a || [] // 不一定是 空数组, 只是假设
  ```
- `arguments.callee`表示正在执行的函数(仅适用于非严格模式)  
  ```js
  function fn(a) {
    arguments.callee(1) // 相当于fn(1)
  }
  ```
- 函数的参数需要严格判断类型(因为JS是弱类型),所以面试才问那么多判断变量类型的题目,用TS就没有这个烦恼了...
- 高阶函数：操作函数的函数,将函数作为参数,并返回一个新函数
- 一般的对象假如没有实现`.toString()`,那么会默认从`Object.prototype`继承`toString()`方法,这个方法的返回结果是`[object Object]`,一般无意义,所以需要自己写
- `.valueOf()`在进行数学运算和关系运算时会被调用
- 除了设置类的属性可以是不可变的(只读+不可删除),也可以设置类以及类的实例也是不可变的(用过`Object.defineProperties`以及`Object.create()`)
- 对象指定属性不可写以及不可配置
  ```js
  // 将o的指定名字的(或所有)的属性设置为不可写的和不可配置的
  function freezeProps(o) {
    let props = (arguments.length === 1) ? Object.getOwnPropertyNames(o) : [].slice.call(arguments, 1) // 如果只有一个参数,使用所有的属性,否则传入指定名字的属性
    props.forEach(n => {
      if (!(Object.getOwnPropertyDescriptors(o, n).configurable)) {
        return
      }
      Object.defineProperty(o, n, {
        writable: false,
        configurable: false
      })
    })
    return o
  }
  ```
  更简单的方法是`Object.seal()`与`Object.freeze()`,如下:
  ```js
  Object.freeze(obj) 
  方法可以冻结一个对象。一个被冻结的对象再也不能被修改；冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后该对象的原型也不能被修改。freeze() 返回和传入的参数相同的对象。

  Object.seal(obj)
  方法封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要可写就可以改变。
  ```
- `Object.defineProperty()`与`Object.defineProperties()`默认属性描述符都是`true`
- 防止类的扩展(禁止添加类属性)
  ```js
  let a = {
    name: 'xiao ming',
    age: 18
  }
  Object.preventExtensions(a)
  a.sex = 'man'
  console.log(a.sex) // undefined
  ```
- 浏览器本质上是简易的操作系统
- 了解一下这种写法: `<script type='text/script'></script>`
- 要获取`<script>`里的文本,一样可以使用DOM操作获取;  
  也可以手动设置`script`标签的`src`属性, 浏览器会去下载的,由此模拟异步加载  
  ```js
  let s = document.createElement('script')
  s.setAttribute('src', './js2.js')
  console.log('in html\n', new Date(), '\n',s)
  document.body.appendChild(s)
  // document.getElementsByTagName('head')[0].appendChild(s)
  ```
- `javascript:js代码`这一用法可以放在使用常规URL的任意地方,例如`<a>`标记里的`href`属性,`<form>`里的`action`属性,甚至`window.open()`的参数,  
  但是不同的浏览器对`javascript:js代码`的有不同的执行行为,Firefox会执行URL里的代码并重载页面并显示`javascript:js代码`的返回值,Chrome则会执行`javascript:js代码`但忽略返回值  
  `javascript:js代码`是web早期的用法,现在主要用于浏览器`书签`
- 在当前页面嵌入另一个窗体(如`<iframe>`),那么当前页面的JS代码与被嵌入页面的JS代码会有不同的全局对象
- `document.write()`早期的DOM操作方式,如:`document.write('<div id="test">123123123123</div>')`,第一次执行会重新渲染整个文档
- H5新增的写在`<script>`上的属性:
  * 渲染引擎遇到 `<script>` 标签会停下来，等到执行完脚本，继续向下渲染
  * `defer`延迟脚本的执行(直到文档的载入和解析完成才开始操作),执行顺序有序;
  * `async`是浏览器尽可能早地执行脚本(而不用在下载脚本时阻塞DOM文档解析),异步脚本,执行顺序可能无序;
  * `async`的优先级高于`defer`
  * 加载 es6模块的时候设置`type=module`，异步加载不会造成阻塞浏览器，页面渲染完再执行，可以同时加上async属性，异步执行脚本    
   （利用顶层的this等于undefined这个语法点，可以侦测当前代码是否在 ES6 模块之中）
- `document.readyState`有三个值,表示文档的解析状态:
  * loading / dom正在加载, 
  * interactive / 可交互. 文档已被解析，"正在加载"状态结束，但是诸如图像，样式表和框架之类的子资源仍在加载。
  * complete / 完成。文档和所有子资源已完成加载。表示 load 状态的事件即将被触发。
  * 当这个属性的值变化时，document 对象上的readystatechange 事件将被触发
    ```js
    document.onreadystatechange = function () {
      if (document.readyState === "interactive") {
        initApplication();
      }
    }
    ```
- 标准模式：渲染模式将兼容CSS标准; 怪异模式：渲染模式的选择依赖于`DOCTYPE`  
  ```js
  // 判断方法
  if (document.compatMode === 'CSS1Compat') {
    console.log("标准模式")
  } else if (document.compatMode === undefined) {
    console.log('怪异模式')
  }
  ```
- 浏览器嗅探也可以在服务端完成：客户端在首都字段添加`User-Agent`并传至服务端,服务端根据`User-Agent`头部可以有选择地返回特定的JS代码
- 客户端JS时间线
  * 浏览器创建Document对象,并开始插入DOM元素节点,此时`document.readystate === loading`
  * 遇见`<script>`标签时：
    - 假如`<script>`标签没有`async`与`defer`,那么浏览器会暂停解析HTML,然后开始下载`<script>`里的文件并执行,`<script>`里的文件可以操作DOM
    - 假如`<script>`标签有`async`,那么浏览器不会暂停解析HTML,但也会开始下载`<script>`里的文件并执行,`<script>`里的文件不可以操作DOM
    - 当文档解析完成,`document.readyState`变成`interactive`
    - 假如`<script>`标签有`defer`,那么异步脚本会在这个时间里执行,它能操作DOM,但不能使用`document.write()`
    - `Document`上触发`DOMContentLoader`,标识浏览器进入事件监听
    - 浏览器继续加载其他资源(图片/音视频),完成加载时,`document.readyState`变成`complete`,并触发`window`对象的事件`load`
    - 此时将开始调用异步事件,如：网络事件...
- 解决同源策略问题的方式：
  * 跨域
  * 跨文档：postMessage()
- `window.location === document.location`  
  `document.URL === document.location.href`  
- `window.navigator`浏览器信息,有属性：  
  * window.navigator.userAgent
  * window.navigator.appName  
  * window.navigator.cookieEnabled 返回true/false，true时表示浏览器可永久保存cookie
  * window.navigator.onLine        返回true/false，表示是否联网
  * window.navigator.geolocation   
- 浏览器弹窗
  * alert(字符串)
  * confirm(字符串)
  * prompt(字符串, 默认值)
- `Object.prototype`直接为`Object`添加方法的方式并不推荐,因为会导致`for...in...`遍历时遍历到`Object.prototype`上的方法,这是不希望看到的,  
  因此需要`Object.defineProperty()`安全地将方法扩充至`Object.prototype`
- 鸭式辩型
  `像鸭子一样走路，游泳并且嘎嘎叫的鸟就是鸭子`
- JS的枚举类 enumeration  
  * 可以使用对象obj模拟实现,并用`Object.freeze()`禁止修改(虽然JS权威指南书P217有更权威的)
  * `Object.freeze()`与`Object.seal()`的异同：
    - 都无法新增属性，无法删除属性
    - `Object.freeze()`无法修改属性,`Object.seal()`可以修改属性