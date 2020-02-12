# EventLoop

## JS 是单线程
- 因为 JS 目的是与用户交互，操作 DOM 等，因此只能单线程。否则会带来很复杂的同步问题 
- 比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？
- 为了利用多核CPU的计算能力，HTML5提出**Web Worker**标准，允许JavaScript脚本创建多个线程
- 但是子线程完全受主线程控制，且不得操作DOM。所以，这个新标准并没有改变JavaScript单线程的本质
  
## 如何解决单线程带来的性能问题
- 异步
- Web Worker

## JS 内存模型
- 堆（Heap）：存放变量，对象
- 调用栈（Call Stack）：执行主线程任务（同步任务）
- 任务队列（Queue）：异步任务与定时任务
- 运行机制（以异步执行为例子）
  * 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
  * 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
  * 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
  * 主线程不断重复上面的第三步（即宏任务执行完成后，检查是否有微任务。若有，执行微任务；否则，重复宏任务）

## 自测题目
说明并解释下面题目输出什么：
```js
console.log('script start')
setTimeout(function() {
  console.log('timeout1')
}, 10)
new Promise(resolve => {
    console.log('promise1')
    resolve()
    setTimeout(() => console.log('timeout2'), 10)
}).then(function() {
    console.log('then1')
})
console.log('script end')
// script start, promise1, script end, then1, timeout1, timeout2
```
## 疑难点
- 定时任务 setTimeout 与 setInterval是在同步代码全部执行后，微任务（Job）也执行后再执行的，且执行完成后到规定时间才返回主线程
- JS执行时，在遇到 IO任务（Ajax从网络获取数据）等耗时任务、DOM事件的回调函数，定时器时，会把回调函数扔进任务队列
- 调用栈执行完成后会检查并执行任务队列中的任务，任务队列遵循先进先出
- 任务队列中的任务分两种：宏任务，微任务
  * MacroTask（Task）
    - setTimeout, setInterval, setImmediate, requestAnimationFrame, I/O, UI rendering
  * MicroTask（在ES2015规范中称为Job）
    - process.nextTick, Promise, Object.observe, MutationObserver
  * 事件循环的每个阶段（macrotask）之间都会执行 microtask，事件循环的开始会先执行一次 microtask
- 对于回调任务，不管它写在哪里，即使是第一行，都是在同步任务之后执行
- 创建Promise实例是同步执行的, Promise的异步体现在`.then()`以及`.catch()`

## 更复杂的题目
```js
async function async1() {           
  console.log("async1 start");  //(2)        
  await  async2();            
  console.log("async1 end");   //(7)    
}        
async  function async2() {          
  console.log( 'async2');   //(3)     
}       
console.log("script start");  //(1)      
setTimeout(function () {            
  console.log("settimeout");  //(8)      
},0);        
async1();        
new Promise(function (resolve) {           
  console.log("promise1");   //(4)         
  resolve();        
}).then(function () {            
  console.log("promise2");    //(6)    
});        
console.log('script end'); //(5)
```
- 先按顺序执行同步代码  从‘script start‘开始，
- 执行到setTimeout函数时，将其回调函数加入队列(此队列与promise队列不是同一个队列，执行的优先级低于promise)。
- 然后调用async1()方法，await async2();//执行这一句后，输出async2后，await会让出当前线程，将后面的代码加到任务队列中，然后继续执行test()函数后面的同步代码
- 继续执行创建promise对象里面的代码属于同步代码，promise的异步性体现在then与catch处，所以promise1被输出，然后将then函数的代码加入队列，继续执行同步代码，输出script end。至此同步代码执行完毕。
- 开始从队列中调取任务执行，由于刚刚提到过，setTimeout的任务队列优先级低于promise队列，所以首先执行promise队列的第一个任务，因为在async函数中有await表达式，会使async函数暂停执行，等待表达式中的 Promise 解析完成后继续执行 async 函数并返回解决结果。
- 所以先执行then方法的部分，输出promise2，然后执行async1中await后面的代码，输出async1 end。。最后promise队列中任务执行完毕，再执行setTimeout的任务队列，输出settimeout。
- setTimeout(fn,0)的含义是指某个任务在主线程最早可得的空闲时间执行。它在“任务队列”的尾部添加一个事件，因此要等到同步任务和“任务队列”现有的时间处理完才会得到执行。

## 参考
- [JavaScript是多线程还是单线程？](https://blog.csdn.net/qq_36995542/article/details/80007381)
- [深入理解JavaScript事件循环机制](https://www.cnblogs.com/yugege/p/9598265.html)
- [JavaScript事件循环(Event Loop)机制](https://www.cnblogs.com/yzg1/p/7514514.html)
- [promise、setTimeout、async的先后执行顺序](https://www.cnblogs.com/xiaoan0705/p/10451360.html)
- [8张图帮你一步步看清 async/await 和 promise 的执行顺序](https://segmentfault.com/a/1190000017224799)
- [如何解释Event Loop面试官才满意？](https://zhuanlan.zhihu.com/p/72507900?utm_source=wechat_session&utm_medium=social&utm_oi=726559320958853120)