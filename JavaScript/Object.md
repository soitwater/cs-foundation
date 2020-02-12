# Object

## 前置知识
- 可枚举属性与不可枚举属性
  * 由属性 enumerable 值决定
  * 可枚举性决定该属性能否 
    1. for...in...
    2. Object.keys()
    3. JSON.stringify
   
## 属性描述符
  描述符|含义|说明
  -|-|-
  value|值
  writable|是否可以修改属性的值|相当于为属性添加一个空的setter或返回TypeError
  configurable|是否可以用defineProperty()方法修改属性描述符|①单向操作，无法将configurable由  false变true，②但还是可以把writable由true变false，③无法 delete myObj.a，因为该属性无法配置
  enumerable|该属性是否可以出现在遍历中(for...in...等)|... ...  
  &nbsp;  
  - 从ES5开始，所有属性都具备属性描述符  


## 测试模板
```js
let arr = ["a", "b", "c"] // 定义一个 Array 对象
let obj = { foo: "bar", baz: 42 } // 定义一个 Object 对象
let ArrayLike = { 0 : "a", 1 : "b", 2 : "c"} // 定义一个  类数组 对象
let anObj = { 100: 'a', 2: 'b', 7: 'c' } // 类数组 对象, 随机 key 排序 
```
## Object.keys 与 Object.getOwnPropertyNames
- Object.keys方法和Object.getOwnPropertyNames方法都用来遍历对象的属性
- 参数都是对象，返回一个数组
- 区别：
  * Object.keys方法只返回可枚举的属性
  * Object.getOwnPropertyNames方法还返回不可枚举的属性名
  ```js
  Object.keys(arr) // ["0", "1", "2"]
  Object.getOwnPropertyNames(arr) // ["0", "1", "2", "length"]
  ```

## Object.assign(target, source1, sources2, ...)
- 用于对象合并, 返回 target
- 同名属性, source 会覆盖 target
- 一级属性深拷贝, 其他属性浅拷贝
- 只拷贝source中可枚举的属性(`enumerable`), 会调用 `[[Get]]`以及`[[Set]]`
- 原始类型会被包装为对象

## Object.create(obj)
- `Object.create(obj.prototype)`相当于`new obj()`
  ```js
  Object.create =  function (o) {
    var F = function () {};
    F.prototype = o;
    return new F();
  }
  ```

## Object.defineProperties(obj, props)
```js
var obj = {}
Object.defineProperties(obj, {
  'property1': {
    value: true,
    writable: true
  },
  'property2': {
    value: 'Hello',
    writable: false
  }
})
```
## 待补充(根据MDN)

## 参考
- [Object 对象](https://wangdoc.com/javascript/stdlib/object.html#object-%E7%9A%84%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95)
- [MDN-Object](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)
- [21 分钟精通前端 Polyfill 方案](https://segmentfault.com/a/1190000010106158?utm_source=tuicool&utm_medium=referral)
- [js学习日记-new Object和Object.create到底干了啥](https://www.cnblogs.com/94pm/p/9113434.html)