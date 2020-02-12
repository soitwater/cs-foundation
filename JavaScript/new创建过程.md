# new创建过程

```js
function Person() {
  this.name = name
  this.age  = age
}
var person = new Person()
```
- 系统会在内部创建一个this对象，
- this的属性__proto__转到构造函数Preson的prototype上面，
- 将Person构造函数内其他属性、方法添加到this对象上，
- 最后再隐式的把this对象return出来，

## 实现
简洁版
```js
function newFunc(constructor){
  //第一步：创建一个空对象obj 
  var obj = {};
  //第二步：将构造函数 constructor的原型对象赋给obj的原型
  obj.__proto__ = constructor.prototype;
  //第三步：将构造函数 constructor中的this指向obj,并立行构造函数内部的操作
  constructor.apply(obj);
  //第四步：返回这个对象
  return obj;
}
```
```js
function newObj(obj, ...args) {
  // 创建一个空对象
  let newObj = new Object()
  // 将newObj的this指向obj
  obj.apply(newObj, args)
  // 将newObj的原型指向构造函数的prototype
  newObj.__proto__ = obj.prototype
  // 返回
  return newObj
}
function Person(name) {
  this.name = name
}
Person.prototype.say = function () {
  console.log(`i'm ${this.name}`)
}
// 测试一下
let a = newObj(Person, '小花')
console.log(a.name) // 小花
a.say() // i'm 小花
```
假如new创建对象时有其他返回值...
```js
function newObj(obj, ...args) {
  // 创建一个空对象
  let newObj = new Object()
  // 将newObj的原型指向构造函数的prototype
  newObj.__proto__ = obj.prototype
  // 将newObj的this指向obj
  let ret = obj.apply(newObj, args)
  // 判断返回值 如果返回值为对象就返回这个对象 返回值不为对象 就返回创建的对象
  // 这一句其实是回答：
  // new 一个构造函数，如果构造函数返回 return {}、return null，会出现什么情况
  return typeof ret === 'object' ? ret : newObj
}
```
new 构造函数的返回值若是“object”类型，则返回该“object”；否则，返回原创建的对象
```js
function A (val) {
	this.val = val
	return {newVal: "newVal"} // {newVal: "newVal"}
	return null // A { val: 'xiao' }
	return 1    // A { val: 'xiao' }
	return true // A { val: 'xiao' }
}

let a = new A ("xiao")
console.log(a)
```

## 参考
- [js中new一个对象的过程](https://blog.csdn.net/qian3223/article/details/82500857)
- [浅谈JavaScript new对象的四个过程](https://blog.csdn.net/zhouziyu2011/article/details/60143385)
- [模拟实现new](https://www.jianshu.com/p/febd82436720)