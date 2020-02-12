# call与apply与bind

## 三者共同点
- 都可以改变this的指向  
  * 格式：方法.call/apply/bind(obj, 参数...)  
  * 解析：相当于 方法(参数...)，当方法里遇到`this`时，`this`指的是`obj`而非你看到的上下文,也相当于`obj.方法(参数...)`

## 不同点
- call/apply都是返回值，bind是返回函数

## 例子 
```js
// 下面的两个函数没有 this，用 call/apply 都和没用一样
function add(a, b) {
  alert(a + b);
}
function sub(a, b) {
  alert(a - b);
}
add.call(sub,3,1); // 结果为4
add.apply(sub,[3,1]); // 结果为4
add.bind(sub)(3,1); // 结果为4 ，写成 add.bind(sub,3,1) 也行
```

## 实现
思路只有一个:
- 确定是否 `this` 的类型是 `function`
- 根据`context.fn(...args)`,一次确定`context`,`context.fn`, `args`(需判定是否存在`arguments[1]`)
- 返回
```js
Function.prototype.myCall = function (context) {
  var context = context || window
  // 给 context 添加一个属性
  // getValue.call(a, 'yck', '24') => a.fn = getValue
  context.fn = this
  // 将 context 后面的参数取出来
  var args = [...arguments].slice(1)
  // getValue.call(a, 'yck', '24') => a.fn('yck', '24')
  var result = context.fn(...args)
  // 删除 fn
  delete context.fn
  return result
}
```
```js
Function.prototype.myApply = function (context) {
  var context = context || window
  context.fn = this
  var result
  // 需要判断是否存储第二个参数
  // 如果存在，就将第二个参数展开
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }
  delete context.fn
  return result
}
```
```js
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    // 即play.myBind(person)返回的是一个函数
    // 有可能是 new play.myBind(person)() 的情况
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    // args 是 play.myBind(person) 里的 person; arguments 是 play.myBind(person)() 里的 ()
    return _this.apply(context, args.concat(...arguments))
  }
}

// test
let person = {
  name: "xiaoming",
  say () {
    console.log(`say >>>  ${this.name}`)
  }
}
function play () {
  console.log(`play >>>  ${this.name}`)
}
play.myBind(person)()
```

## 参考
- [JS中的apply,call,bind深入理解](https://www.cnblogs.com/jone-chen/p/5033682.html)
- [手动实现call() , apply() , bind()](https://blog.csdn.net/qq_36367995/article/details/81319852)