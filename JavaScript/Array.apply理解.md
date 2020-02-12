# Array.apply理解
## 问题一
vue官方文档有下面一段代码
```js
Array.apply(null, {length: 20}).map(function () {
  return createElement("p", "hi")
})
```
为什么是{length: 20} 而非 new Array(20)?
## 解答
- Array.apply(this的指向, 类数组)
  * 这里的类数组要求：是Object类型，具有属性 length
- 以下写法等价
```javascript
let iAmArray = function () {
  return agrguments
}
iAmArray(1, 2, 3)
iAmArray.call(null, 1, 2, 3)
iAmArray.apply(null, [1, 2, 3])
```
## 问题二
为什么后者无法执行 map() ?
```js
console.log(Array.apply(null, Array(20)).map(() => 8)) // (20) [8 × 20]
console.log(new Array(20).map(() => 8)) // []
```
## 解答
new Array(20)所创建的数组：无元素，无索引，仅一个length属性  
Array.apply(null, Array(20))创建的数组：有元素（值都为 undefined），有索引，有length  
因此，因为后者为 [] ，执行 map()后不会有结果 
## 参考
- [关于apply，Array.apply(null, {length:5})怎么理解](https://segmentfault.com/q/1010000006793990)  
- [js中Array.apply(null,Array(3))与Array(3)的区别是什么？](https://www.jianshu.com/p/6c7d0b18d4ca)