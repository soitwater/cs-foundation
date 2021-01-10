# ES6
## 释意
- `ECMAScript`在每年`6`月发布一次正式版本
- 提案阶段
  * Stage 0 - Strawperson（展示阶段）
  * Stage 1 - Proposal（征求意见阶段）
  * Stage 2 - Draft（草案阶段）
  * Stage 3 - Candidate（候选人阶段）
  * Stage 4 - Finished（定案阶段）

## 知识点
* 解构赋值
* 属性名表达式
* Array拓展
* Object拓展
* Function拓展  
&nbsp;  
* rest参数(...)
* 扩展运算符(...)  
&nbsp;  
* iterator遍历器
* Generator函数  
&nbsp;  
* Promise
* Symbol
* Proxy
* Set & Map
* class
* module
* let & const

## 数组的解构赋值（本质是：模板匹配）
```js
let a = 1
let b = 2
等价于 let [a, b] = [1, 2]
```
```js
对象的解构赋值
let {foo, bar} = {bar: "bbb", "foo": "fff"}
```
```js
数组的解构赋值 与 对象的解构赋值 的区别在：
数组的解构赋值 在赋值时需要严格按照顺序，而对象的解构赋值在赋值时则不需要
```

### 一些解构的特殊情况
```js
当变量名与属性名不同的情况（如下，a是属性名，b是变量名）参考：真正被赋值的是变量名
let {a:b} = {a:"aaa"}
console.log(a) // referenceError: a is not defined
console.log(b) // "aaa"
```
```js
对复杂嵌套结构的对象的解构
let obj = {
  p: ["hello", {y: "world"}]
}
let {p: [x, {y}]} = obj
console.log(x) // "hello"
console.log(y) // "world"
```
```js
对已经声明的变量进行解构赋值，需要注意:
let x; 
{x} = {x: 1}  // SyntaxError  因为{x} 会被理解成是一个代码块
应写作：
({x} = {x:1})  // 注意：书上写 ({x}) = {x:1} 也是可以的, 但实际测试报错
```
```js
交换变量的值
[x, y] = [y, x]
```
```js
从函数返回多个值，
函数只能返回1个值，若需返回多个，需使用数组or对象
使用解构可以方便的获取这些值
function f () {
  return [1, 2, 3]
}
let [a, b, c] = [1, 2, 3]
console.log(a, b, c)
```
```js
函数参数的定义(尤其在提取JSON对象中的数据时)
function f ({x, y, z}) {
  return
}
f({x: 1, y: 2, z: 3})
```
```js
遍历map
let map = new Map()
map.set("key1", "value1")
map.set("key2", "value2")
map.set("key3", "value3")
for (let [key, value] of map) { // 获取 key 与 value
  console.log(key + ":" + value)
}
for (let [, value] of map) { // 获取 value
  console.log(value)
}
```
```js
加载模块时，指定输入哪些方法
const {fn1, fn3} = require("model")
```

## 数组的拓展
- Array.from() 将两类对象直接转换为真正的数组，  
包括类似的数组的对象(如,document.querySelectorAll()所返回的类数组对象)，以及可遍历的对象
  ```js
  let ps = document.querySelectorAll("p")
  Array.from(ps).forEach(p => console.log(p))
  ```
- Array.of() 将一串数值转化为数组
  ```js
  console.log(Array.of(1,2,3)) // [ 1, 2, 3 ]
  console.log(Array()) // []
  console.log(Array(3)) // [undefined, undefined, undefined]
  console.log(Array(1,2,3)) // [1, 2, 3]
  ```
  主要是为了解决Array()的设计缺陷:  
  * Array() 无参则创建空数组
  * Array(number) 1参 则创建长度为number且值都为`empty`的数组
  * Array(1,2,3) 不少于2个参数, 则创建的数组是…
- 数组实例的find()和findIndex()
  ```js
  let a1 = [1,2,3].find((value, index, arr) => { // 找出第一个符合条件的数组元素
    return value === 3
  })
  let a2 = [1,2,3].findIndex((value, index, arr) => { // 找出第一个符合条件的数组元素的索引
    return value === 3
  })
  console.log(a1, a2) // 3, 2
  ```
- 数组实例的fill——使用定值填充一个数组
  ```js
  console.log([1, 2, 3].fill(3)) // [3, 3, 3]
  console.log([1, 2, 3].fill(4, 1, 2)) // [1, 4, 3] 1表示起始位置, 2表示结束位置(不包含该位置)
  ```
- 数组实例的 entries()  keys()  values()  
  这3个方法都返回一个遍历器, 可用于 for…of 循环
  ```js
  for (let index of ["a", "b"].keys()) {
    console.log(index)
  }
  for (let [index, value] of ["a", "b"].entries()) {
    console.log(index, value)
  }
  Array.Observe()  Array.unobserve()
  ```
- 监听数组的4种变化：add delete update splice(length属性发生变化)

<hr/>

## 对象的拓展
- Object.is()  
  与 === 类似，比较两个值是否严格相等，但有两点不同
  ```js
  console.log(+0 === -0) // true 
  console.log(NaN === NaN) // false
  console.log(Object.is(+0, -0)) // false
  console.log(Object.is(NaN, NaN)) // true
  ```
- Object.assign()
  浅复制
  ```js
  let a = {}
  let b = {x: {key: "1"}}
  Object.assign(a, b)
  b.x = "2"
  console.log(a) // {x: {key: "1"}}
  ```
- Object.setPrototypeOf(object, prototype) 
  ```js
  // 设置实例对象object的对象原型为prototype。类似  object.__proto__ = prototype，但js标准没有__proto__，虽然许多浏览器都支持
  Object.getPrototypeOf(obj) // 获取对象的原型
  function Fn1 () {
    return
  }
  Fn1.prototype.say = () => console.log("say something.")
  let a = {
    x: "1",
  }
  console.log(Object.getPrototypeOf(a)) // {}
  Object.setPrototypeOf(a, Fn1.prototype)
  a.say() // "say something"
  console.log(Object.getPrototypeOf(a)) // Fn1 { say: [Function] }
  ```
- 属性名表达式：  
  使用方括号包裹，支持变量名，支持表达式
  ```js
  let attrName = "attribute type1 "
  let a = {
    x: "1", // x --> 1
    [attrName]: "2", // attribute type1 --> 2
    [attrName + "A"]: "3", // attribute type1 A --> 3
  }
  for (let item in a) {
    console.log(item, " --> ", a[item])
  }
  ```
- Symbol: 新的原始数据类型，创建方式如下，特点是其值唯一，常用作对象的属性名以避免重复
  ```js
  let s1 = Symbol()
  let s2 = Symbol("s2's name")
  let s3 = Symbol()
  console.log(s1, s2, s3, s1 == s3, s1 === s3) // Symbol() Symbol(s2's name) Symbol() false false
  let a = {
    [s1]: "symbol1"
  }
  console.log(a[s1], a.s1) // symbol1 undefined
  ```
- Proxy 代理，拦截器
  ```js
  let person = {
    name: "xiaoming"
  }
  let intercept = {
    get (target, property) { // target 即 本例中的 person
      if (property in target) {
        return target[property]
      } else {
        throw new Error("目标对象不含该属性")
      }
    },
  }
  let proxy = new Proxy(person, intercept) // person目标对象, intercept拦截器,proxy是person的代理 
  console.log(proxy.name) // "xiaoming" (注意这里是proxy，而不是person)
  console.log(proxy.age) // 报错
  ```
- Object.observe()  Object.unobserve()      
  监听对象的变化  在ES7中被移除，且在2015年底被从V8引擎中移除


- 函数的拓展  
  函数参数的默认值  
  常用于构造函数中的默认参数，为“默认参数”形式的函数参数都是可选的，不带默认值的参数则是必选参数
  ```js
  function Fn (z, x = 0, y = 1) {
    this.x = x
    this.y = y
    this.z = z
  }
  let f = new Fn(2)
  console.log(f.x, f.y, f.z) // 0 1 2
  ```
  可以利用参数默认值，设置某一参数初始化时必须赋值，否则报错
  ```js
  function throwIfMiss () {
    throw new Error ("该参数不能为空") // 注意: 是throw 不是return
  }
  function paramMustSet (x = throwIfMiss()) {
    return x
  }
  paramMustSet() // 报错
  ```
- rest参数 (即 … )
  可以表示剩下的所有参数，也相当于其他语言的不定长度的参数列表, 代替原来的 arguments 特性
  表示：将参数列表转化为数组
  ```js
  function push (arr, ...items) {
    items.forEach((item) => {
      arr.push(item)
    })
    return arr
  }
  let arr = [0, 1, 2]
  console.log(push(arr, 3, 4, 5)) // [ 0, 1, 2, 3, 4, 5 ]
  ```
- 扩展运算符（即 … ）
  是rest参数的逆运算，rest参数用于函数声明创建，扩展运算符用于函数调用
  表示：将一个数组转化为以逗号为间隔的参数列表
  ```js
  let a = [0, 1, 2]
  let b = [3, 4, 5]
  console.log([...a, ...b, 6]) // [ 0, 1, 2, 3, 4, 5, 6 ]
  ```

<hr/>  

## Set 与 Map
- 快速去除数组中重复元素的方法
  ```js
  let a = [0, 1, 2, 3, 3, 4, 4, 5]
  function f (arr) {
    return Array.from(new Set(arr))
  }
  console.log(f(a)) // [ 0, 1, 2, 3, 4, 5 ]
  ```
- Map 表示 键值对的集合，与JS的对象类似，  
  但JS对象的键都是字符串，Map的键允许：对象，数组  
  ```js
  let m = new Map()
  let obj = {x: "1"}
  let arr = [1, 2]
  let num = 1
  m.set(obj, "objValue")
  m.set(arr, "arrValue")
  m.set(num, "numValue")
  console.log(m.get(obj), m.get(arr), m.get(num)) // objValue arrValue numValue
  ```
- Map 原生提供3个遍历器  
  keys()  values()  entries()  
  WeakMap 与 Map 类似，但 WeakMap 只接受 对象 ( null 除外) 作为键名  
  注意，WeakMap 不接受原始类型的值 作为键名  
  WeakMap的键名是弱引用，可能会被自动回收；若回收，则WeakMap自动移除对应的键值  

- 其他
  ```js
  使用Set进行包含性检测很快
  setOfWords.has("csy")
  Set 不支持索引
  
  new Set()
  new Set(任何可遍历数据)
  s.size
  s.has(元素)
  set.add(元素)
  set.delete(元素)
  s.clear()
  s.keys()
  s.values()
  s.entries()
  
  Map 的方法
  new Map()
  new Map(二元数组或另一个Map)  // 二元数组形如: let a = [['a',1],['b',2]]
  map.size
  map.has(key)
  map.get(key)
  map.set(key, value)
  map.delete(key)
  map.clear()
  map.keys()
  map.values()
  map.entries()
  
  WeakMap 只支持 new、has、get、set 和 delete。
  WeakSet 只支持 new、has、add 和 delete。
  WeakSet 的值和 WeakMap 的键必须是对象。
  还要注意，这两种弱集合都不可迭代
  ```

<hr/>

## iterator 与 for of 与 for in
- for in 遍历键名：  常用于对象，若用于数组，则遍历索引
- for of 遍历键值：  常用于数组等支持iterator接口的，若用于对象则报错
- 一个对象，只要部署了 next 方法，就是 具有 iterator 接口，就可以使用 for of 遍历
- ES6 遍历器协议：
  * 部署了next方法的对象，就具备遍历器功能。
  * next 方法必须返回一个包含value和done两个属性的对象。
  * value表示当前遍历位置的值，done表示一个布尔值，表遍历是否结束。
- iterator 实现，以及让对象支持 for of    
  主要是为Object的原型 添加 Symbol.iterator 的方法  
  （Array  String Set Map的原型都有 Symbol.iterator 方法）  
- iterator原理：
  ```js
  function makeIterator (array) {
    let nextIndex = 0
    return {
      next () {
        return nextIndex < array.length ? 
          {value: array[nextIndex++], done:false}: {value: undefined, done: true}
      }
    }
  }
  let it = makeIterator(["a", "b"])
  console.log(it.next().value) // "a"
  console.log(it.next().value) // "b"
  console.log(it.next().done) // true
  function Fn (name, age) {
    this.name = name
    this.age = age
  }
  
  Object.prototype[Symbol.iterator] = function() {
    let _this = this
    let index = 0
    let length = Object.keys(_this).length
    return {
      next:() => {
        let value = Object.keys(_this)[index] // 这里的value表示遍历时的key
        let done = (index >= length)
        index++
        return {value,done}
      }
    }
  }
  let fn = new Fn("小明", 45)
  for (let key of fn) {
    console.log(key, "--", fn[key])
  }
  ```
  
<hr/>

## Generator 函数

- function* 函数名，yield “状态名” 含义同return但表示暂停，.next()遍历到下一状态，next() 依旧遵循遍历协议其返回的值形式为{value: "yield 的返回值", done: true / false}  
  function* () 必须使用.next() 才会开始执行
  ```js
  function* generator () {
    yield "value1"
    yield "value2"
    yield "value3"
    return "ending"
  }
  let gene = generator() // 必须使用这种表达式，表示引用
  console.log(gene.next()) // { value: 'value1', done: false }
  console.log(gene.next()) // { value: 'value2', done: false }
  console.log(gene.next()) // { value: 'value4', done: false }
  console.log(gene.next()) // { value: 'ending', done: true }
  ```
- yield 语句本身没有返回值，或者说总是返回 undefined    
  .next(param) 可以有一个参数, 表示上一 yield 语句的 返回值  
    ```js
  function* g () { // 无限执行的遍历器
    for (let i = 0;true;i++) {
      let rest = yield i // 在遇到 yield 时就暂停, 此时 .next() 返回 {value: i, done: false}; 但是，rest 取值   undefined
      if (rest) {
        i = -1
      }
    }
  }
  let generator = g()
  console.log(generator.next()) // { value: 0, done: false }
  console.log(generator.next()) // { value: 1, done: false }
  console.log(generator.next(true)) // { value: 0, done: false } (此时 yield 语句不再 默认返回 undefined, 而是 true)
  ```
- Generator 函数 常用于 改写异步操作的回调函数，实际操作中一般让 yield 返回 Promise 对象
  ```js
  function* numbers () {
    let file = new FileReader("./test2.html")
      try {
        while (!file.eof) {
          yield file.readLine()
        }
      } finally {
        file.close()
      }
  }
  ```
- for of 循环可以自动遍历 Generator 函数，且不需要 .next()
  ```js
  function* fibonacci () { // 斐波那契数列
    let [prev, curr] = [0, 1]
    for (;;) {
      [prev, curr] = [curr, prev + curr]
      yield curr
    }
  }
  let f = fibonacci()
  for (n of f) {
    if (n < 1000) {
      console.log(n) 
    }
  }
  ```
- 如果 yield 命令后面跟着 遍历器，则使用 yield* ，表示 yield 嵌套，可用于遍历二叉树等
  ```js
  let delegatedIterator = (function* () {
    yield "Hello "
    yield "Bye "
  }())
  let delegatingIterator = (function* () {
    yield "Greetings "
    yield* delegatedIterator
    yield "OK,Bye~ "
  }())
  for (let value of delegatingIterator) {
    console.log(value)
  }
  ```
- 中序遍历二叉树  
  下面的代码没有考虑 makeTree(array) 时, array 的长度问题，是吗??
  ```js
  function Tree (left, label, right) {
    this.left = left
    this.label = label
    this.right = right
  }
  function* midOrder (tree) {
    if (tree) {
      yield* midOrder(tree.left)
      yield tree.label
      yield* midOrder(tree.right)
    }
  }
  function makeTree (array) {
    if (array.length === 1) {
      return new Tree(null, array[0], null)
    } else {
      return new Tree(makeTree(array[0]), array[1], makeTree(array[2]))
    }
  }
  let tree = makeTree([[["a"], "b", ["c"]], "d", [["e"], "f", ["g"]]])
  let result = []
  for (let node of midOrder(tree)) {
    result.push(node)
  }
  ```

<hr/>

## Promise 的错误具有冒泡性质, 会一直向后传递，直到被捕获为止  
所以这么写 Promise 链： doSomething().then().then().catch()  
还可以使用 async 以及 await  

<hr/>

## class 以及 module
```js
class Father {
  constructor (name, age) {
    this.name = name
    this.age = age
  }
  toString () {
    console.log(`我是${this.name}, 我今年${this.age}岁.`)
  }
}
class Child extends Father {
  constructor (name, age) {
    super(name) // 相当于 super.constructor(name)
    this.age = age
  }
}
let f = new Father ("父亲", 39)
f.toString() // 我是父亲, 我今年39岁.
let c = new Child("孩子", 15)
c.toString() // 我是孩子, 我今年15岁.
```

<hr/>

## export
```js
export let firstName = "a"
export {firstName}
export default 

import {firstName} from "./module.js"
import {firstName as anotherName} from "./module.js"

module all from "./module.js" // 整体导入
```
