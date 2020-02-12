# JavaScript语言精粹

## p8
数字的写法: 1e2 === 100, 2e3 === 2000
## p9
字符串不可变,所有对字符串的操作都是返回新的字符串
## p12
if(xxx) 里的 xxx, 假如为：`字符串'false'`(此时是`true`), 所有的对象(即使看似是空对象), 那其实都是`true`
## p33
- 数字取整
  ```js
  Number.prototype.Integer = function () { // 不要使用箭头函数,因为会丢失 this
    console.log('this >>>', this, this.valueOf()) // this >>> [Number: -3.2] -3.2
    return Math[this >= 0 ? 'floor' : 'ceil'](this.valueOf())
  }
  let a = new Number(-3.2)
  let b = -3.2
  console.log('a >>>', a.Integer()) // -3
  console.log('b >>>', b.Integer()) // -3
  ```
- 在为原型链添加方法前,应该检查原型链上是否有同名的方法
  ```js
  Function.prototype.method = function (name, func) {
    if (!this.prototype[name]) {
      this.prototype[name] = func
    }
  }
  ```
## 42
级联就是JS的链式写法
## 44
函数可以使用对象(键值对方式)去记忆之前的函数操作结果,例如递归。
## 47
每一次new 一个新对象都会运行类似的一些代码`this.prototype = {constructor: this}`
## 59
JS的数组长度并不严格,length的值是这个数组的最大整数的属性名加上1
## 61
使用typeof检测数据类型——数组
```js
let isArray = function (value) {
  return value && // 不接受 null 和其他值为假的值
    typeof value === 'object' && // 数组类型至少为 object
    typeof value.length === 'number' && // 数组需要length属性
    typeof value.splice === 'function' && // 数组需要有方法 splice
    !(Object.getOwnPropertyDescriptors(value).length.enumerable) // 数组的length属性不可遍历
}
```
## 102
js有一个机制,会自动插入分号以修正有缺损的程序,然而有时会有bug
```js
return // 因为没有分号,这里会默认在 return 这一行的末尾加上 分号,下面的对象会忽略,导致返回的不是对象,而是 undefined
{
  name: 'xiao ming'
}
```
## 105
检测一个变量是数字类型
```js
function isNumber (value) {
  return typeof value === 'number' && isFinite(value) // isFinite() 会排除 NaN以及inFinite, 但会将类似 '20' 的转化为 20,所以需要 typeof
} 
```
## 107
统计单词个数
```js
function fn (text) {
  let words = text.toLowerCase().split(/[\s,.]+/)
  let count = {}
  for (let i = 0;i < words.length;i++) {
    if (count[words[i]]) {
      count[words[i]] += 1
    } else {
      count[words[i]] = 1
    }
  }
}
```

