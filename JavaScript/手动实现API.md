# 手动实现API


## 判断对象里的数据类型
不推荐使用`Object.prototype.toString.call()`去检测基本类型的的变量的类型, 因为`.call()`会对第一个参数进行装箱操作
```js
const isType = type => target => `[object ${type}]` === Object.prototype.toString.call(target)
const isArray = isType("Array")
console.log(isArray([]))
```

## 实现数组的some方法
- 注意方式
  * 获取调用该函数的数组 `arr = [].slice.call(this)`
  * `arr.hasOwnProperty(i)`
  * `fn.call(context, arr[i], arr)`
```js
function selfSome(fn, context) {
  let arr = Array.prototype.slice.call(this)
  if (!arr.length) return false
  let flag = false
  for (let i = 0; i < arr.length; i++) {
    if(!arr.hasOwnProperty(i)) continue
    let res = fn.call(context, arr[i], i, arr)
    if (res) {
      flag = true
      break
    }
  }
  return flag
}
```

## 实现数组的reduce方法
> 当初始值不存在时，下标从 1 开始计算，当初始值存在时，下标从 0 开始计算  
```js
const selfReduce = function(fn, initialValue) {
  let arr = Array.prototype.slice.call(this)
  if (initialValue) arr.unshift(initialValue)
  let res = arr[0]
  for (let i = 0;i < arr.length - 1;i++) {
    if (!arr.hasOwnProperty(i + 1)) continue
    res = fn.call(
      null,
      res,
      arr[i + 1],
      initialValue === undefined ? i + 1 : i,
      arr
    )
  }
  return res
}
```

## 实现数组的flat方法(数组扁平化)
```js
// 使用 reduce、concat 和递归无限反嵌套多层嵌套的数组
var arr1 = [1,2,3,[1,2,3,4, [2,3,4]]];

function flattenDeep(arr1) {
   return arr1.reduce((acc, val) => Array.isArray(val) ? acc.concat(flattenDeep(val)) : acc.concat(val), []);
}
flattenDeep(arr1);
// [1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```

## 实现 ES6 的 class 语法
> - ES6 的 class 内部是基于寄生组合式继承，它是目前最理想的继承方式，  
> - 通过 Object.create 方法创造一个空对象，并将这个空对象继承 Object.create 方法的参数，再让子类（subType）的原型对象等于这个空对象，  
> - 就可以实现子类实例的原型等于这个空对象，而这个空对象的原型又等于父类原型对象（superType.prototype）的继承关系  
> - 而 Object.create 支持第二个参数，即给生成的空对象定义属性和属性描述符/访问器描述符，  
> - 我们可以给这个空对象定义一个 constructor 属性更加符合默认的继承行为，同时它是不可枚举的内部属性（enumerable:false）  
> - 而 ES6 的 class 允许子类继承父类的静态方法和静态属性，而普通的寄生组合式继承只能做到实例与实例之间的继承，  
> - 对于类与类之间的继承需要额外定义方法，这里使用 Object.setPrototypeOf 将 superType 设置为 subType 的原型，从而能够从父类中继承静态方法和静态属性
```js
function inherit(subType, superType) {
  subType.prototype = Object.create(superType.prototype, {
    constructor: {
      enumerable: false,
      configurable: true,
      writable: true,
      value: superType.constructor
    }
  })
  Object.setPrototypeOf(subType, superType)
}
```

## 斐波那契数列优化
利用函数记忆，将之前运算过的结果保存到对象键值对中  
缺点是
```js
const fibonacci1 = function(n) {
  if (n < 1) throw new Error ('error param')
  if (n === 1 || n === 2) return 1
  return fibonacci1(n - 1) + fibonacci1(n - 2) 
}
const memory = function (fn) {
  let obj = {}
  return function (n) {
    if (!obj[n]) obj[n] = fn(n)
    return obj[n] 
  }
}
const fibonacci2 = memory(fibonacci1)
```

## 私有变量的实现
```js
// Proxy 代理所有含有"_"开头的变量,使其不可被外部访问
const proxy = function(obj) {
  return new Proxy(obj, {
    get (target, key) {
      if (key.startsWith('_')) {
        throw new Error('private key')
      }
      return Reflect.get(target, key)
    },
    ownKeys(target) {
      return Reflect.ownKeys(target).filter(key => !key.startsWith('_'))
    }
  })
}
```
```js
class Person {
  constructor(name) {
    let _name = name
    this.getName = function() { // 缺点是 私有变量的读取函数必须写在 constructor 构造函数中,比较不简洁
      return _name
    }
  }
}
```

## 私有方法的实现
使用Symbol做key，避免被外部调用or覆盖.也不会被`遍历`/`JSON.stringify()`等放入字符串中(但会被`getOwnpropertySymbols`取到)  
如： 
```js
let s = new Slider()
s[Object.getOwnPropertySymbols(slider.__proto__)[0]] = function() {
  console.log(`此函数被重写了`)
}
```
```js
const render = Symbol("render")
const event = Symbol("event")
class Slider {
  constructor(opts) {
    this.opts = opts
    if (!opts.container) {
      throw 'container 必须被配置'
    } else {
      this[render](opts)
      this[event](opts)
    }
  }
  [render](opts) {

  }
  [event](opts) {

  }
}
```

## 洗牌算法
// 原地(遍历数组,将当前位置的数组元素与随机位置的数组元素交换位置)
```js
function shuffle(arr) {
  for (let i = 0;i < arr.length;i++) {
    let randomIndex = i + Math.floor(Math.random() * (arr.length - i))
    [arr[i], arr[randomIndex]] = [arr[randomIndex], arr[i]]
  }
  return arr
}
```
// 非原地
```js
function shuffle(arr) {
  let _arr = []
  while (arr.length) {
    let randomIndex = Math.floor(Math.random() * arr.length)
    _arr.push(arr.slice(randomIndex, 1)[0])
  }
  return _arr
}
```

## 优雅使用`async/await`(自动为所有ES7异步函数添加try块)
```js
async function errorCaptured(asyncFn) {
  try {
    let res = await asyncFn()
    return [null, res]
  } catch (e) {
    return [e, null]
  }
}
// 使用方法
let [err, res] = await errorCaptured(asyncFn)
```

## promisify(将所有的NodeJS风格的回调函数都改成Promise)
```js
function promisify(asyncFunc) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      args.push(function callback(err, ...value) {
        if (err) {
          return reject(err)
        }
        return resolve(...values)
      })
      asyncFunc.call(this, ...args)
    })
  }
}

const fsp = new Proxy(fs, {
  get(target, key) {
    return promisify(target[key])
  }
})

// 使用
await fsp.readFile('./promiseify.js', 'utf-8')
```
> promisify 函数是将回调函数变为 promise 的辅助函数，适合 error-first 风格（nodejs）的回调函数，  
> 原理是给 error-first 风格的回调无论成功或者失败，在执行完毕后都会执行最后一个回调函数，我们需要做的就是让这个回调函数控制 promise 的状态即可  
> 这里还用了 Proxy 代理了整个 fs 模块，拦截 get 方法，使得不需要手动给 fs 模块所有的方法都包裹一层 promisify 函数，更加的灵活

## 实现`JSON.stringify`
```js
function jsonstringify(obj) {
  let type = typeof obj;
  if (type !== 'object') {
    if (/string|undefined|function/.test(type)) {
      obj = '"' + obj + '"'
    }
    return String(obj)
  } else {
    let arr = Array.isArray(obj);
    let json = [];
    for (let i in obj) {
      let j = obj[i];
      let type = typeof j
      if (/string|undefined|function/.test(type)) {
        j = '"' + j + '"'
      } else if (type == 'object') {
        j = jsonstringify(j)
      }
      json.push((arr ? "" : '"' + i + '":') + String(j));
    }
    return (arr ? "[" : "{") + String(json) + (arr ? "]" : "}")
  }
}

jsonstringify({ x: 1 })  //"{"x":1}"
jsonstringify({ x: undefined })  //"{"x":"undefined"}"
jsonstringify([false, 'false', 'true', true, 12])  //"[false,"false","true",true,12]"
```

## 手动实现数字转货币
- 题目
  * 输入：12345，输出：12,345  
  * 输入：2345.6789，输出：2,345.678,9  
  * 要求：使用正则和非正则两种方式实现
- 实现
  ```js
  function ParseMoney(str) {
    let regexp = /\B(?=(\d{3})+\b)/g
    let arr = str.split(".")
    let intergetNum = arr[0].replace(regexp, ',')
    let decimalNum = ""
    if (arr[1]) {
      decimalNum = arr[1].replace(regexp, ',') // 这里其实是错误的
    }
    return intergetNum + "." + decimalNum
  }
  
  console.log(ParseMoney("123456789.987654321"))
  console.log(ParseMoney("123456789"))
  ```

## 手写时间戳转换函数
- 题目
  ```
  发布新闻时需要提醒发布的时间。  
  写一个函数，传递一个参数为时间戳，完成时间的格式化。  
  如果发布一分钟内，输出：刚刚；  
  n 分钟前发布，输出：n分钟前；  
  超过一个小时，输出：n小时前；  
  超过一天，输出：n天前；  
  但超过一个星期，输出发布的准确时间  
  ```
- 答案
  ```js
  function time (lastTime) {
    let now = (new Date()).valueOf()
    let diff = (now - lastTime) / 1000
    let result = ""
    if (diff < 60) {
      result = "刚刚"
    } else if (diff < 3600) {
      result = Math.floor(diff / 60) + "分钟前"
    } else if (diff < 86400) {
      result = Math.floor(diff / 3600) + "小时前"
    } else if (diff < 604800) {
      result = Math.floor(diff / 86400) + "天前"
    } else {
      result = (new Date()).toLocaleString()
    }
    return result
  }
  
  console.log(time(new Date(2018,7,3,9,13,0).valueOf()))
  console.log(time(new Date(2019,7,3,9,13,0).valueOf()))
  ```

## 手动实现trim()
```js
String.prototype.trim=function(){
  return this.replace(/(^\s*)|(\s*$)/g, "");

String.prototype.ltrim=function(){
  return this.replace(/(^\s*)/g, "");

String.prototype.rtrim=function(){
  return this.replace(/(\s*$)/g, "");
}
```

## 手动实现EventEmitter
```js
class EventEmitter{
  constructor(){
    this._events = {}
  }
  on(event,callback){  //监听event事件，触发时调用callback函数
    let callbacks = this._events[event] || []
    callbacks.push(callback)
    this._events[event] = callbacks
    return this
  }
  off(event,callback){  //停止监听event事件
    let callbacks = this._events[event]
    this._events[event] = callbacks && callbacks.filter(fn => fn !== callback)
    return this
  }
  emit(...args){ //触发事件，并把参数传给事件的处理函数
    const event = args[0]
    const params = [].slice.call(args,1)
    const callbacks = this._events[event]
    callbacks.forEach(fn => fn.apply(this, params))
    return this
  }
  once(event,callback){ //为事件注册单次监听器
    let wrapFanc = (...args) => {
      callback.apply(this, args)
      this.off(event,wrapFanc)
    }
    this.on(event,wrapFanc)
    return this
  }
}
```
验证
```js
function trigger () {
  console.log("emit >>> trigger")
}
function onceTrigger () {
  console.log("emit >>> onceTrigger")
}
let eventEmitter = new EventEmitter()
eventEmitter.on("trigger", trigger)
eventEmitter.emit("trigger")
eventEmitter.off("trigger", trigger)
eventEmitter.emit("trigger")

eventEmitter.once("onceTrigger", onceTrigger)
eventEmitter.emit("onceTrigger")
eventEmitter.off("onceTrigger")
eventEmitter.emit("onceTrigger")
```

## 手动实现轮播
```html
<!DOCTYPE html>
<html lang="zh-cmn-Hans">
<head>
  <meta charset="utf-8"/>
  <meta http-equiv="X-UA-Compatible" content="IE=Edge, chrome=1"/>
  <style>
    html , body {margin:0; padding:0; font-size:100px;}
    #wrap {
      position: relative;
      height: 4rem;     
    }
    .banner {position:absolute;height:100%;width:100%;}
    .banner .banner__pic {position:absolute;width:100%;height:100%;opacity:0;transition:opacity 1s ease;}
    .banner > .banner__pic--1 {background:purple; opacity:1;}
    .banner > .banner__pic--2 {background:pink;}
    .banner > .banner__pic--3 {background:greenyellow;}
    .banner > .banner__pic--4 {background:gray;}
    .controls { /*左右页面切换按钮的包围盒*/
      --height:0.8rem;
      height: var(--height);
      width: 100%;
      position: absolute;
      top:50%;
      transform: translateY(-50%);

      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .controls__prev, .controls__next { /*左右切换页面的按钮*/
      width: .5rem;
      height: .5rem;
      border-radius: 50%;
      background: green;
    }
    .tag { /*底部分页按钮的包围盒*/
      width: 1rem;
      height: .14rem;
      position:absolute;
      top:100%;
      left:50%;
      transform: translate(-50%, -150%);
      display: flex;
      justify-content: space-around;
      align-items: center;
      background:green;
    }
    .tag .tag__page { /*分页按钮*/
      height: 0.1rem;
      width: 0.1rem;
      border-radius: 50%;
      background: yellow;
    }
  </style>
</head>
<body>
  <div id="wrap">
    <div class="banner">
      <!-- <div class="banner__pic banner__pic--4"><img src=""/></div> -->
      <div class="banner__pic banner__pic--1"><img src=""/></div>
      <div class="banner__pic banner__pic--2"><img src=""/></div>
      <div class="banner__pic banner__pic--3"><img src=""/></div>
      <div class="banner__pic banner__pic--4"><img src=""/></div>
      <!-- <div class="banner__pic banner__pic--1"><img src=""/></div> -->
    </div>
    <div class="controls">
      <div class="controls__prev"></div>
      <div class="controls__next"></div>
    </div>
    <div class="tag">
      <span class="tag__page"></span>
      <span class="tag__page"></span>
      <span class="tag__page"></span>
      <span class="tag__page"></span>
    </div>
  </div>
  <script>
    let nextControl = document.querySelector(".controls__next")
    let prevControl = document.querySelector(".controls__prev")
    let bannerPictureList = document.getElementsByClassName("banner__pic")
    let currentBannerPicIndex = 0 // 初始状态下,展示第0张图片
    nextControl.addEventListener("click", () => changeBannerPicture("next"))
    prevControl.addEventListener("click", () => changeBannerPicture("prev"))

    function changeBannerPicture (type) {
      let newPicIndex 
      switch (type) {
        case "next":
          newPicIndex = currentBannerPicIndex + 1
          newPicIndex === bannerPictureList.length ? newPicIndex = 0 : 1
          break
        case "prev":
          newPicIndex = currentBannerPicIndex - 1
          newPicIndex === -1 ? newPicIndex = bannerPictureList.length - 1 : 1
          break
      }
      bannerPictureList[newPicIndex].style.opacity = 1
      bannerPictureList[currentBannerPicIndex].style.opacity = 0
      currentBannerPicIndex = newPicIndex
    }
  </script>
</body>
</html>
```

## 参考
- [一个合格的中级前端工程师必须要掌握的 28 个 JavaScript 技巧](https://zhuanlan.zhihu.com/p/67612791)
- [数字与金额数字转换的正则表达式](https://www.cnblogs.com/MonaSong/p/6684673.html)
- [原生JS正则实现trim()](https://www.cnblogs.com/dll-ft/p/5845686.html)
- [使用原生js实现轮播图效果](https://link.zhihu.com/?target=https%3A//segmentfault.com/a/1190000010581158)
- [原生js系列之无限循环轮播组件](https://segmentfault.com/a/1190000012432451)
