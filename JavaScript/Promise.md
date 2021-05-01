# Promise

## 基础
```js
// Promise 通常放进一个function中，因为new Promise() 之后不需要调用该Promise实例就能开始执行
function asyncMethod1 () {
  let promise = new Promise ((resolve, reject) => {
    // new Promise() 括号里是一个function
    // 该 function 的函数体就是所需的异步操作
    // 该 function 有两个参数，resolve 与 reject
    // resolve 与 reject，它们分别是成功后、失败后的回调函数参数
    setTimeout(() => {
      console.log('异步1 --> 2秒后输出此句.') 
      if (false) {
        // 这里是执行函数的意思
        // 但因为是异步，可以理解为 给then传递此promise成功的状态fillfiled，并传递值'异步1 --> Promise成功后的回调函数参数'
        resolve('异步1 --> Promise成功后的回调函数参数') 
      } else {
        reject('异步1 --> Promise失败后的回调函数参数')
      }
    }, 2000)
  })
  // 方便使用链式写法, 即方便 .then()以及 .catch()
  return promise
}

function asyncMethod2 () {
  let promise = new Promise ((resolve, reject) => {
    setTimeout(() => {
      console.log('异步2 --> 2秒后输出此句.')
      if (true) {
        resolve('异步2 --> Promise成功后的回调函数参数')
      } else {
        reject('异步2 --> Promise失败后的回调函数参数')
      }
    }, 2000)   
  })
  return promise
}

function asyncMethod3 () {
  let promise = new Promise ((resolve, reject) => {
    setTimeout(() => {
      console.log('异步3 --> 2秒后输出此句.')
      if (true) {
        resolve ('异步3 --> Promise成功后的回调函数参数')  
      } else {
        reject ('异步3 --> Promise失败后的回调函数参数')
      }
     }, 2000)
  })
  return promise
}

asyncMethod1()
.then((success) => {
// .then() 的作用是接受前面 return 返回的数据, 
// 即捕捉Promise的成功状态fullfiled，或Promise的失败状态rejected
// .then() 括号里是两个参数，前者是成功后回调函数，后者是失败后回调 
  console.log(data + " 第一个then")
  return asyncMethod2() // 这里会执行 asyncMethod2()，然后返回 asyncMethod2()此方法的返回值
}, (err) => {
  console.log(err + " 第一个then")
  return asyncMethod2()
})
.then((data) => {
  console.log(data + " 第二个then")
  return asyncMethod3() 
})
.then((data) => {
  console.log(data + " 第三个then")
})
// 总结--上述的异步函数链的阐述
// asyncMethod1() 是先 return promise, 然后到第1个then声明括号里的两个函数体，接着then处于捕捉 asyncMethod1() 状态值的状态
// asyncMethod1()里setTimeout在2秒后执行，先console.log()，再执行reject('异步1 --> Promise失败后的回调函数参数')
// 因为第1个then括号里的函数体已经声明完成了，所以reject('异步1 --> Promise失败后的回调函数参数')可以被执行
// 之后在第一个then()里，也就是reject('异步1 --> Promise失败后的回调函数参数')在console.log(err + " 第一个then")之后，就是return asyncMethod2()，
// return asyncMethod2()会进入 asyncMethod2()里
// 同理，先return promise，所以 return asyncMethod2() 是 return了第二个Promise
// 然后再在第2个then声明函数体，第2个then监听状态，执行asyncMethod2()里的setTimeout，console.log()，执行第二个then的 resolve('异步2 --> Promise成功后的回调函数参数')
// return asyncMethod3()同理
// 进入 asyncMethod3()，return promise，进入第三个then，then括号里参数声明，then监听状态，进入asyncMethod3()的setTimeout,console.log(),resolve ('异步3 --> Promise成功后的回调函数参数')   
```
```js
// 除了.then(), 还有.catch()，它的作用是：
// ①then监听到rejected时，代替.then()的第二个参数，相当于reject() 
// ②then监听到fullfield时，若在.then()的第一个参数执行时发生错误，.catch() 可以捕捉这个错误
asyncMethod1()
.then((data) => {
  console.log (data)
})
.catch((err) => {
  console.log (err)
})
```
```js
// .all 提供并行执行异步操作的功能，直到最慢的异步执行完才会执行.then
// then中的data是一个数组，then监听三个异步函数的状态值。
// 一旦有Promise报错，立刻执行then()里的第二个参数函数
// 但then的第一个函数参数会等所有Promise都执行完才会执行
// 注意下面的all是Promise的静态方法
// 注意all括号里一个数组
Promise
.all ([asyncMethod1(), asyncMethod2(), asyncMethod3()])
.then((data) => {
  console.log(data) // data 是一个数组, 元素是resolve()方法括号里的参数
}, (err) => {
  console.log(err) // err 只会捕捉第一个报错的Promise
})

// 类似的Promise.race 是赛跑的意思
// Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。
Promise.race([asyncMethod1(), asyncMethod2(), asyncMethod3()])
.then((data) => {
  console.log ('resolve里的参数', data) 
}, (err) => {
  console.log ('reject里的参数', err) 
})
```
new Promise()中若出现错误是不会报错的

## 例题
### 1
```js
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('success')
  }, 1000)
})
const promise2 = promise1.then(() => {
  throw new Error('error!!!')
})

console.log('promise1', promise1)
console.log('promise2', promise2)

setTimeout(() => {
  console.log('promise1', promise1)
  console.log('promise2', promise2)
}, 2000)
```
执行结果  
promise 有 3 种状态：pending、fulfilled 或 rejected。状态改变只能是 pending->fulfilled 或者 pending->rejected，状态一旦改变则不能再变
```js
promise1 Promise { <pending> }
promise2 Promise { <pending> }
promise2 抛出的错误
promise1 Promise { <resolved>'success' }
promise2 Promise { <rejected> Error: error!!!}
```
### 2
```js
Promise.resolve()
  .then(() => {
    return new Error('error!!!')
  })
  .then((res) => {
    console.log('then: ', res)
  })
  .catch((err) => {
    console.log('catch: ', err)
  })
```
执行结果  
.then 或者 .catch 中 return 一个 error 对象并不会抛出错误，所以不会被后续的 .catch 捕获，需要改成其中一种：
* return Promise.reject(new Error('error!!!'))
* throw new Error('error!!!')
```js
then: Error: error!!!
    at Promise.resolve.then (...)
    at ...
```
### 3 两道死循环的题目
```js
const promise = Promise.resolve()
  .then(() => {
    return promise
  })
promise.catch(console.error)
```
```js
process.nextTick(function tick () {
  console.log('tick')
  process.nextTick(tick)
})
```
### 4
```js
Promise.resolve(1)
  .then(2)
  .then(Promise.resolve(3))
  .then(console.log)
```
.then 或者 .catch 的参数期望是函数，传入非函数则会发生值穿透。
```js
1
```
### 5
```js
Promise.resolve()
  .then(function success (res) {
    throw new Error('error')
  }, function fail1 (e) {
    console.error('fail1: ', e)
  })
  .catch(function fail2 (e) {
    console.error('fail2: ', e)
  })
```
执行结果  
.then 的第二个处理错误的函数捕获不了第一个处理成功的函数抛出的错误，而后续的 .catch 可以捕获之前的错误。
```js
fail2: Error: error
    at success (...)
    at ...
```

## 手动实现maxRequest
- 实现maxRequest，成功后resolve结果，失败后重试，尝试超过一定次数才真正的reject
  ```js
  function maxRequest(fn, maximum) {
    return new Promise((resolve, reject) => {
      function helper(num) {
        Promise
          .resolve(fn())
          .then((val) => {
            resolve(val);
          })
          .catch((err) => {
            if(num - 1 > 0) {
              console.log('重试: ', num);
              helper(num - 1);
            } else {
              reject(err);
            }
          })
      };

      helper(maximum);
    });
  }

  function getData(num) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject('错误: ' + num);
      }, 500);
    });
  }

  maxRequest(getData.bind(null, 666), 10);
  ```

## 实现Promise.all
```js
// Promise.all接收一个参数, 参数的类型是`数组`
function PromiseAll(promises) {
  // 如果全部resolve(), 那么Promise.all的返回结果将是一个数组
  const values = [];
  let count = 0;
  return new Promise((resolve, reject) => {
    promises.forEach((promise, index) => {
      Promise
        .resolve(promise())
        .then((val) => {
          count++;
          values[index] = val;
          // 全部执行完毕则resolve()
          if(count === promises.length) {
            console.log('全部正确: ' + values.join(','))
            resolve(values);
          }
        })
        .catch((err) => {
          // Promise.all遇到第一个reject,会直接跳出
          reject(err);
        })
    });
  })
}

function getData(num) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(num);
    }, 500);
  });
}

function getWrongData(num) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject('错误: ' + num);
    }, 500);
  });
}

PromiseAll([
  getData.bind(null, 10),
  getData.bind(null, 20),
  getData.bind(null, 30),
]);

PromiseAll([
  getData.bind(null, 10),
  getWrongData.bind(null, 20),
  getData.bind(null, 30),
]);
```

## 实现Promise.allSettled
```js
// Promise.allSettled接收一个参数, 参数的类型是`数组`
function PromiseAllSettled(promises) {
  const values = [];
  let count = 0;
  return new Promise((resolve, reject) => {
    promises.forEach((promise, index) => {
      Promise
        .resolve(promise())
        .then((val) => {
          values[index] = {
            value: val,
            reason: null,
          };
        })
        .catch((err) => {
          values[index] = {
            value: null,
            reason: err,
          };
        })
        .finally(() => {
          count++;
          // 全部执行完毕则resolve()
          if(count === promises.length) {
            console.log('返回: ' + values);
            resolve(values);
          }
        })
    });
  });
}

function getData(num) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('正确: ' + num);
      resolve(num);
    }, 1000);
  });
}

function getWrongData(num) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('错误! ' + num);
      reject('错误: ' + num);
    }, 1000);
  });
}

PromiseAllSettled([
  getData.bind(null, 10),
  getData.bind(null, 20),
  getData.bind(null, 30),
]);

PromiseAllSettled([
  getData.bind(null, 10),
  getWrongData.bind(null, 20),
  getData.bind(null, 30),
]);
```
