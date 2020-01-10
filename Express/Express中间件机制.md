# Express中间件机制

## 解析
- 一个请求发送到服务器后，它的生命周期是：先收到request（请求），然后服务端处理，response（响应）  
- 其中“服务端处理”可分配成几个部分来做，而每个部分就是一个中间件  
- express内部维护一个数组，每一次`app.use(fn)`都会将`fn`插入到该数组中  
- 通过`next()`可以执行函数数组中的下一个函数  

## 实现
```js
/**
 * 仿照express实现中间件的功能
 *
 * Created by BadWaka on 2017/3/6.
 */

let http = require('http');

/**
 * 仿express实现中间件机制
 *
 * @return {app}
 */
function express() {
  let funcs = []; // 待执行的函数数组
  let app = function (req, res) {
    let i = 0;
    function next() {
      let task = funcs[i++];  // 取出函数数组里的下一个函数
      if (!task) {    // 如果函数不存在,return
        return;
      }
      task(req, res, next);   // 否则,执行下一个函数
    }
    next();
  }
  /**
   * use方法就是把函数添加到函数数组中
   * @param task
   */
  app.use = function (task) {
    funcs.push(task);
  }
  return app;    // 返回实例
}

let app = express();

function middlewareA(req, res, next) {
  console.log('middlewareA before next()');
  next();
  console.log('middlewareA after next()');
}

function middlewareB(req, res, next) {
  console.log('middlewareB before next()');
  next();
  console.log('middlewareB after next()');
}

function middlewareC(req, res, next) {
  console.log('middlewareC before next()');
  next();
  console.log('middlewareC after next()');
}

app.use(middlewareA);
app.use(middlewareB);
app.use(middlewareC);
app()
```
输出
```js
middlewareA before next()
middlewareB before next()
middlewareC before next()
middlewareC after next()
middlewareB after next()
middlewareA after next()
```

## 参考
- [Express中间件的原理及实现](https://www.jianshu.com/p/797a4e38fe77)