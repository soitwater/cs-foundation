# Array

## 数组API
```js
// 判断是否为数组
arr instanceof Array
Array.isArray(arr)
// 转为字符串
arr.toString()
arr.join("可选参数")
// 返回数组本身
arr.valueOf()
// 数组的添加与删除
arr.push()
arr.pop()
arr.unshift() # 在首位插入, 返回的是新长度length
arr.shift() # 返回的是头部元素
// 数组排序
arr.reverse()
arr.sort()
arr.sort((a,b)=>b-a)
// 数组元素操作 (会改变原数组)
arr1.concat(arr2) 连接两个数组
arr.slice(开始索引, 结束索引) 可为负数
arr.splice(开始索引, 删除几个, 替换内容1, 替换内容2 ...) // 会改变原数组，返回被删除的数组
arr.indexOf(元素)
arr.lastIndexOf(元素)
// 数组迭代(遍历)  (返回新数组, 不改变原数组)
arr.every(function (元素, 索引值, 数组本身) {
  // ...
  // 对符合条件的 element —— return true
  // 全部true才为true, 否则中断并返回false
})
arr.filter(function (元素, 索引值, 数组本身) {
  // ...
  // 对符合条件的 element —— return true
  // 返回 结果为true 的元素构成的数组
})
arr.map(function (元素, 索引值, 数组本身) {
  // ...
  // return element
  // 返回加工后的新数组
})
arr.some(function (元素, 索引值, 数组本身) {
  // ...
  // 对符合条件的 element —— return true
  // 一个符合则中断并返回 true
})
// 数组清空
arr = []
```

## 数组的复制
```js
ES5 ：a2 = a1.slice()
ES5 : 遍历数组的
ES5: let arr1 = [].concat([1,2,3])

ES6: Object.assign(arr, [1, 2, 3])
ES6：const a1 = [1, 2];  const a2 = [… a1]
以上方法都是一维数组的深拷贝，二维数组深拷贝见下
let arr = [[1]] 
let re=[]; 
for(let i=0;i<arr.length;i++) { 
  let [...arr1]=arr[i];
  re.push(arr1); 
}
```

## reduce
### 概念
与`.map()`类似，都是遍历数组，对数组每一元素进行操作。  
不同的是`.reduce()`每次遍历牵涉的不仅仅单一一个数组元素，还与上一个相关  
每次回调的`return`会作为下一次的`pre`  
有`初始值`会比无`初始值`多一次遍历  
```js
arr.reduce(function (pre, 当前值, 当前值的索引, 源数组) {
  // 若存在“初始值”，则当前值从数组的第一个开始，pre为“初始值”，
  // 若不存在，则当前值从数组的第二个开始，pre为数组第一个
}, [初始值])
```
### 应用
#### 累加
```js
arr.reduce((prev, cur) => prev + cur)
```
#### 求极值
```js
arr.reduce((prev, cur) => Math.max(prev, cur))
```
### 数组去重
```js
arr.reduce((prev, cur) => {
 !prev.includes(cur) && prev.push(cur) 
 return prev
}, [])
```
#### 实现map函数
```js
Array.prototype._map = function(cb, context) {
  if(typeof cb === 'function') {
    let arr = Array.prototype.slice.call(this) // 获取需要被执行map()操作的数组(假如其为类数组,还将强制转换其为数组)
    let mapedArr = []
    for (let i = 0;i < arr.length;i++) {
      if (!arr.hasOwnProperty(i)) { // 稀疏数组中部分索引无值(empty)(此时则跳过)
        continue
      }
      mapedArr.push(cb.call(context, arr[i], i, this))
    }
    return mapedArr
  } else {
    throw new Error(cb, " is not a function")
  }
}
```
```js
let arr = new Array(1,2,3)
Array.prototype._map = function(cb, context) {  // context, 这个参数应该可以省略, 或者让 context 的默认值为 undefined
  if(typeof cb === 'function') {
    let arr = Array.prototype.slice.call(this) // 获取需要被执行map()操作的数组(假如其为类数组,还将强制转换其为数组)
    return arr.reduce((prev, item, index) => {
      return [...pre, fn.call(context, cur, index, this)]
    }, [])
  } else {
    throw new Error(cb + ' is not function')
  }
}

let res = arr._map(n => n * 2)
console.log(res) // [ 2, 4, 6 ]
```
#### 实现filter函数
```js
let arr = [1, 2, 3, 4, 5];

Array.prototype._filter = function(cb) {
  if(typeof cb === 'function') {
    let arr = Array.prototype.slice.call(this);
    return arr.reduce((prev, item, index, array) => {
        cb(item, index, array) ? prev.push(item) : null
        return prev
    }, [])
  } else {
    throw new Error(cb + ' is not function')
  }
}

let res = arr._filter(n => n>2)
console.log(res) // [ 3, 4, 5 ]
```
#### 实现柯里化
```js
function compose() {
  let args = [].slice.call(arguments)
  return function (x) {
    return args.reduceRight((prev, cur) => {
      return cur(prev)
    }, x)
  }
}

function a(x) {
  return "| " + x + "in a|"
}
function b(x) {
  return "( " + x + "in b )"
}
console.log(">>>> ", compose(a, b)(" begin ")) // >>>>  | (  begin in b )in a|
```
#### 数组扁平化
```js
let arr = [1, 2, '3js', [4, 5, [6], [7, 8, [9, 10, 11], null, 'abc'], {age: 12}, [13, 14]], '[]'];

function flatten(arr) {
  if(Array.isArray(arr)) {
    return arr.reduce((prev, cur) => {
       // 如果遍历的当前项是数组，再迭代展平
      return Array.isArray(cur) ? prev.concat(flatten(cur)) : prev.concat(cur)
    }, [])
  } else {
    throw new Error(arr + ' is not array')
  }
}

console.log(flatten(arr))
```

## 稀疏数组与密集数组
### 稀疏数组
`let arr = Array(3)`即可创建稀疏数组,该数组有长度`3`,但是没有实际的元素(这些元素值连`null`,`undefined`都不是)
### 密集数组
`let arr = Array.apply(null, Array(3))`这种方式可以创建密集数组(元素的值为`undefined`, 可以被遍历)
### 解释
实际,JS中并没有数组,数组其实是带`length`属性的对象

### 参考
- [你应该知道的JS: reduce的n种应用](https://juejin.im/post/5cbd7e085188250a8b7cf5c9)
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
