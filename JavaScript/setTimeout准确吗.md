## setTimeout准确吗

不准确。
```js
console.log(new Date().valueOf())                           // 1
setTimeout(() => console.log(new Date().valueOf()), 0)      // 2
setTimeout(() => console.log(new Date().valueOf()), 1000)   // 3
```
以上程序执行后：2与1相差4ms，3与2相差1000ms  
- 即便setTimeout的延迟为0，浏览器也会等待执行栈为空后`4ms`才执行事件队列
- 包括setTimeout,设置延迟为1000,实际延迟为 1000 + 4