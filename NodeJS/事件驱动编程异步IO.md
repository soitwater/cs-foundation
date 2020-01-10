# 事件驱动编程与异步IO

## 基本概念
- Node的核心理念即为事件驱动编程，即所有的网络请求都被看作事件，监听这些事件，并执行回调函数。  
- Node的另一个特点是异步IO，特点是`Don't call me, I'll call you.`
- 非阻塞模式下，一个线程永远在执行计算操作，这个线程所使用的 CPU 核心利用率永远是 100%，I/O 以事件的方式通知。
- NodeJS解决同步阻塞的方式：单线程、非阻塞的事件编程模式。本质是NodeJS维护一个事件队列,事件队列里都是I/O操作,这些I/O操作会在非I/O操作都完成后再去执行
- 一般的框架技术解决同步阻塞的方式：多线程，一条线程阻塞了没关系，还有其他线程。
- 单线程比多线程的好处：
  * 对操作系统来说，创建一个线程的代价是十分昂贵的，
    - 需要给它分配内存、列入调度，
    - 同时在线程切换的时候还要执行内存换页，CPU 的缓存被清空，
    - 切换回来的时候还要重新从内存中读取信息，破坏了数据的局部性。

## NodeJS的事件循环机制
```js
const EventEmitter = require('events').EventEmitter
const event = new EventEmitter()

event.on('some_event', function(a, b) {
  console.log("some_event callback ", a, b)
})
setTimeout(function() {
  event.emit('some_event', 1, 100)
}, 1000)
```

## 事件驱动events
- 是NodeJS的最核心模块,没有之一,因为 Node.js 本身架构就是事件式,几乎所有模块都依赖于`events`模块
- events 模块只提供了一个对象：  `events.EventEmitter` 。 `EventEmitter`的核心就是事件发射与事件监听器功能的封装
- 一些常用的API
  * `EventEmitter.on(event, listener)`  为指定事件注册一个监听器，接受一个字符串  event  和一个回调函数`listener`
  * `EventEmitter.emit(event, [arg1], [arg2], [...])` 发射  event  事件，传递若干可选参数到事件监听器的参数表
  * `EventEmitter.once(event, listener)`  为指定事件注册一个单次监听器，即监听器最多只会触发一次，触发后立刻解除该监听器
  * `EventEmitter.removeListener(event, listener)` 移除指定事件的某个监听器， listener  必须是该事件已经注册过的监听器
  * `EventEmitter.removeAllListeners([event])` 移除所有事件的所有监听器，如果指定  event ，则移除指定事件的所有监听器

