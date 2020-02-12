# JS经验2

## 1
实现函数
```js
f(1).val = 1
f(1)(2).val = 3
f(1)(2)(3).val = 6
f(10)(100)(1000)(10000).val = 11110
```
实现这样的函数，要求调用深度不限，即可以f(10)(1000)(1)(2)/*中间可能无数个(剩)(余)(的)
```js
function f (val) {
  function inner (innerVal) {
    return f(val + innerVal);
  }
  inner.val = val;
  return inner;
}
```
[阿里前端一面，腾讯前端二面视频面（附带深信服？。。。）](https://www.nowcoder.com/discuss/162963)

### 2
```js
offsetWidth 水平方向 width + 左右padding + 左右border-width
offsetHeight 垂直方向 height + 上下padding + 上下border-width

clientWidth 水平方向 width + 左右padding
clientHeight 垂直方向 height + 上下padding

offsetTop 获取当前元素到 定位父节点 的top方向的距离
offsetLeft 获取当前元素到 定位父节点 的left方向的距离

scrollWidth 元素内容真实的宽度，内容不超出盒子高度时为盒子的clientWidth
scrollHeight 元素内容真实的高度，内容不超出盒子高度时为盒子的clientHeight
```

### 3
```js
var F = function () {}
var f = new F() // 
console.log(f.__proto__) // F.prototype 
console.log(f.__proto__.__proto__) // Object.prototype
console.log(f.__proto__.__proto__.__proto__) // null
```

### 4
为什么要把资源文件放在另一个服务器中？除了服务器压力的问题呢？还有没有其他？
- 实现对cookie的分离，像淘宝cookie很多，把资源文件放在另一个域名中，可以减少主服务器的cookie
- 优化下载，HTTP协议对同一个域名的下载线程数有限制
- 实现了动静分离。当多个域名引入同一个静态资源时可以统一引用静态资源服务器

### 5
```js
null > 0   false
null >= 0  true
null == 0  false
```

### 6
- 判断字母大小写
  ```js
  'a'.charCodeAt(0) > 96 //小写
  'A'.charCodeAt(0) < 90 //大写

  /[A-Z]/.test("A") // true
  /[A-Z]/.test("b") // false

  function IsUpper(code) {
    return code === code.toUpperCase()
  }
  console.log(IsUpper('a')) //false
  console.log(IsUpper('A')) //true

  //大小写字母切换
  s[i]^=(1<<5)
  ```

### 7
删除对象的属性
```js
delete Object.property
```
### 8
- 不使用额外空间即可交换两个数值的方法  
- 下面直接给结论：“基于异或运算”利用了“异或运算”是不进位的二进制加法。它有如下性质：
- 如果 a ^ b = c ，那么 a ^ c = b 与 b ^ c = a 同时成立，利用这一条，可以用于交换两个变量的值。
- 于是，交换两个变量的值，例如 a 和 b，不使用第三个变量，可以这样做：
  ```js
  a = a ^ b
  b = a ^ b
  a = a ^ b
  ```
