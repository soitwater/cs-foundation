# 手动实现Promise
- 原理是观察者模式：注册对应状态的事件处理函数，更新状态时，调用注册过的处理函数


## 实现
```js
// 三种状态
const PENDING = "pending";
const RESOLVED = "resolved";
const REJECTED = "rejected";
// promise 接收一个函数参数，该函数会立即执行
function MyPromise(fn) {
  let _this = this;
  _this.currentState = PENDING;
  _this.value = undefined;
  // 用于保存 then 中的回调，只有当 promise 状态为 pending 时才会缓存，并且每个实例至多缓存一个
  _this.resolvedCallbacks = [];
  _this.rejectedCallbacks = [];
  _this.resolve = function (value) {
    if (value instanceof MyPromise) {
      // 如果 value 是个 Promise，递归执行
      return value.then(_this.resolve, _this.reject)
    }
    setTimeout(() => { // 异步执行，保证执行顺序
      if (_this.currentState === PENDING) {
        _this.currentState = RESOLVED;
        _this.value = value;
        _this.resolvedCallbacks.forEach(cb => cb());
      }
    })
  };
  _this.reject = function (reason) {
    setTimeout(() => { // 异步执行，保证执行顺序
      if (_this.currentState === PENDING) {
        _this.currentState = REJECTED;
        _this.value = reason;
        _this.rejectedCallbacks.forEach(cb => cb());
      }
    })
  }
  // 用于解决以下问题
  // new Promise(() => throw Error('error))
  try {
    fn(_this.resolve, _this.reject);
  } catch (e) {
    _this.reject(e);
  }
}

MyPromise.prototype.then = function (onResolved, onRejected) {
  var self = this;
  // 规范 2.2.7，then 必须返回一个新的 promise
  var promise2;
  // 规范 2.2.onResolved 和 onRejected 都为可选参数
  // 如果类型不是函数需要忽略，同时也实现了透传
  // Promise.resolve(4).then().then((value) => console.log(value))
  onResolved = typeof onResolved === 'function' ? onResolved : v => v;
  onRejected = typeof onRejected === 'function' ? onRejected : r => throw r;
  if (self.currentState === RESOLVED) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      // 规范 2.2.4，保证 onFulfilled，onRjected 异步执行
      // 所以用了 setTimeout 包裹下
      setTimeout(function () {
        try {
          var x = onResolved(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (reason) {
          reject(reason);
        }
      });
    }));
  }
  if (self.currentState === REJECTED) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      setTimeout(function () {
        // 异步执行onRejected
        try {
          var x = onRejected(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (reason) {
          reject(reason);
        }
      });
    }));
  }
  if (self.currentState === PENDING) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      self.resolvedCallbacks.push(function () {
        // 考虑到可能会有报错，所以使用 try/catch 包裹
        try {
          var x = onResolved(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (r) {
          reject(r);
        }
      });
      self.rejectedCallbacks.push(function () {
        try {
          var x = onRejected(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (r) {
          reject(r);
        }
      });
    }));
  }
};
// 规范 2.3
function resolutionProcedure(promise2, x, resolve, reject) {
  // 规范 2.3.1，x 不能和 promise2 相同，避免循环引用
  if (promise2 === x) {
    return reject(new TypeError("Error"));
  }
  // 规范 2.3.2
  // 如果 x 为 Promise，状态为 pending 需要继续等待否则执行
  if (x instanceof MyPromise) {
    if (x.currentState === PENDING) {
      x.then(function (value) {
        // 再次调用该函数是为了确认 x resolve 的
        // 参数是什么类型，如果是基本类型就再次 resolve
        // 把值传给下个 then
        resolutionProcedure(promise2, value, resolve, reject);
      }, reject);
    } else {
      x.then(resolve, reject);
    }
    return;
  }
  // 规范 2.3.3.3.3
  // reject 或者 resolve 其中一个执行过得话，忽略其他的
  let called = false;
  // 规范 2.3.3，判断 x 是否为对象或者函数
  if (x !== null && (typeof x === "object" || typeof x === "function")) {
    // 规范 2.3.3.2，如果不能取出 then，就 reject
    try {
      // 规范 2.3.3.1
      let then = x.then;
      // 如果 then 是函数，调用 x.then
      if (typeof then === "function") {
        // 规范 2.3.3.3
        then.call(
          x,
          y => {
            if (called) return;
            called = true;
            // 规范 2.3.3.3.1
            resolutionProcedure(promise2, y, resolve, reject);
          },
          e => {
            if (called) return;
            called = true;
            reject(e);
          }
        );
      } else {
        // 规范 2.3.3.4
        resolve(x);
      }
    } catch (e) {
      if (called) return;
      called = true;
      reject(e);
    }
  } else {
    // 规范 2.3.4，x 为基本类型
    resolve(x);
  }
}
```

## ES6实现(简化版)
```js
/**
 * Promise类实现原理
 * 构造函数传入一个function，有两个参数，resolve：成功回调; reject：失败回调
 * state: 状态存储 [PENDING-进行中 RESOLVED-成功 REJECTED-失败]
 * doneList: 成功处理函数列表
 * failList: 失败处理函数列表
 * done: 注册成功处理函数
 * fail: 注册失败处理函数
 * then: 同时注册成功和失败处理函数
 * always: 一个处理函数注册到成功和失败
 * resolve: 更新state为：RESOLVED，并且执行成功处理队列
 * reject: 更新state为：REJECTED，并且执行失败处理队列
**/

class PromiseNew {
  constructor(fn) {
    this.state = 'PENDING';
    this.doneList = [];
    this.failList = [];
    fn(this.resolve.bind(this), this.reject.bind(this));
  }

  // 注册成功处理函数
  done(handle) {
    if (typeof handle === 'function') {
      this.doneList.push(handle);
    } else {
      throw new Error('缺少回调函数');
    }
    return this;
  }

  // 注册失败处理函数
  fail(handle) {
    if (typeof handle === 'function') {
      this.failList.push(handle);
    } else {
      throw new Error('缺少回调函数');
    }
    return this;
  }

  // 同时注册成功和失败处理函数
  then(success, fail) {
    this.done(success || function () { }).fail(fail || function () { });
    return this;
  }

  // 一个处理函数注册到成功和失败
  always(handle) {
    this.done(handle || function () { }).fail(handle || function () { });
    return this;
  }

  // 更新state为：RESOLVED，并且执行成功处理队列
  resolve() {
    this.state = 'RESOLVED';
    let args = Array.prototype.slice.call(arguments);
    setTimeout(function () {
      this.doneList.forEach((item, key, arr) => {
        item.apply(null, args);
        arr.shift();
      });
    }.bind(this), 200);
  }

  // 更新state为：REJECTED，并且执行失败处理队列
  reject() {
    this.state = 'REJECTED';
    let args = Array.prototype.slice.call(arguments);
    setTimeout(function () {
      this.failList.forEach((item, key, arr) => {
        item.apply(null, args);
        arr.shift();
      });
    }.bind(this), 200);
  }
}

// 下面一波骚操作
new PromiseNew((resolve, reject) => {
  resolve('hello world');
  // reject('you are err');
}).done((res) => {
  console.log(res);
}).fail((res) => {
  console.log(res);
})
```
解析：  
- ```js
  new PromiseNew((resolve, reject) => { 
    console.log("haha"); 
    resolve("hello") 
  })
  .done((res) => {
    /* xxx */
  })
  ```
  其中的`resolve("hello")`是会在执行到异步链的.done()处才执行的
- 执行顺序：
  * constructor()
  * console.log("haha")
  * resolve () {}
  * 异步链"resolve"，进入done
  * done () {}
  * resolve () {} 里的 setTimeout()
  * 执行doneList中的函数

## 参考
- [模拟ES6中的Promise实现，让原理一目了然](https://www.cnblogs.com/minigrasshopper/p/9141307.html#4268412)