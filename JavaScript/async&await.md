# async & await
- async & await 是对Promise 语法的进一步优化
- 一旦有一个函数用了`async`, 那么调用这个函数的`函数`最好也加上`async`
  
## async
- 强制函数 return Promise 对象  
- return 1 会被包装成`return Promise.resolve(1)`
  * `return Promise.resolve(1)`个人理解(辅助后文理解)是它其实等价于
    ```js
    return new Promise(resolve => {
      resolve(1)
    })
    ```
  * 一个例子
    ```js
    async function fn() {
      await console.log(1)
    }
    // 等价于
    function fn() {
      return new Promise(resolve => {
        resolve(console.log(1)) // 即先`console.log`再resolve(undefine) ?
      })
    }
    ```
## await
- 必须在 async 中使用  

## await究竟在等什么
- await等的是右侧「表达式」的结果  
  * 右侧如果是函数，那么函数的return值就是「表达式的结果」
  * 右侧如果是一个 'hello' 或者什么值，那表达式的结果就是 'hello'
- await等到之后，做了什么事情
  * 分情况，不是promise以及是promise。
  * 如果不是promise , await会阻塞后面的代码，先执行async外面的同步代码，同步代码执行完，再回到async内部，把这个非promise的东西，作为 await表达式的结果
  * 如果它等到的是一个 promise 对象，await 也会暂停async后面的代码，先执行async外面的同步代码，等着 Promise 对象 fulfilled，然后把 resolve 的参数作为 await 表达式的运算结果。
  * 举例
    ```js
    async function async1() {           
      console.log("async1 start");       
      await async2();  // 注意这里的await         
      console.log("async1 end");  // 因为await，这一句会被加入微任务中，具体看EventLoop.md  
    }  
    ```

## 与 Promise.all 的友好协作
```js
let results = await Promise.all([
  fetch(url1),
  fetch(url2),
  ...
])
```

## 捕捉错误
```js
async function f () {
  try {
    let response = await fetch('/no-user-here')
    let user = await response.json()
  } catch(err) {
    // 在fetch和response.json中都能捕获错误
    alert(err)
  }
}
```

## 一个例子
```js
// 尝试 只保留catchWeb的async/await | 只保留getWeb的async/await | catchWeb/getWeb的async/await都保留
```
```js
// axios.js
let axios = require('axios')

async function catchWeb(path) {
  console.log('start in catchWeb')
  await getWeb(path)
  console.log('end in catchWeb')
}

function getWeb(path) {
  console.log(`start in getWeb`)
  axios.get(path)
    .then((res) => {
      console.log(res.data)
    })
  console.log(`end in getWeb`)
}

module.exports.catchWeb = catchWeb
```
```js
// exec.js
let catchWeb = require('./axios.js').catchWeb
console.log(`start in call`)
catchWeb('https://www.baidu.com')
console.log(`end in call`)
```
- 观察控制台输出，尝试理解
  * 异步的本质相当于多开一个线程去处理这一耗时操作(如`IO`),而不必阻塞后面的代码
  * async/await相当于让后面的代码必然被阻塞,要求他们等待那一耗时的操作(即使这一操作全是同步代码,`await`后的代码也将全部被阻塞)
  * await等待的表达式中,内部异步操作前的同步代码是不会被阻塞的

## 一个错题
```js
let data = 'global'
async function out() {
  data = await fn2()
  console.log('up >> ', data) // 这里的data输出的是undefined,因为 fn2() 的返回值是undefined
}
function fn1() {
  return new Promise((resolve, reject) => {
    resolve(+new Date())
  })
}
function fn2() {
  fn1().then(res => {
    return res // 这里的return是.then的return,不是fn2()的return
  })
}
console.log('down: ', out(), data)
```

## 参考
- [Async/await学习](https://segmentfault.com/a/1190000013292562?utm_source=channel-newest)