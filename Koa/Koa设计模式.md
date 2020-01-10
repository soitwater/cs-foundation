# Koa设计模式

## 中间件 - koa-compose
### 特征
- 有统一 context
- 操作先进后出
- 有控制先进后出的机制 next
- 有提前结束机制
### 实现
- 每一个中间件需要封装一个 Promise
- 洋葱模型的先进后出操作，对应Promise.resolve的前后操作
### 例子 - 尝试实现中间件引擎
  ```js
  // 洋葱模型
  function compose(middleware) {
    if (!Array.isArray(middleware)) {
      throw new TypeError('Middleware stack must be an array!');
    }
    
    return function(ctx, next) {
      let index = -1;

      return dispatch(0);

      function dispatch(i) {
        if (i < index) {
          return Promise.reject(new Error('next() called multiple times'));
        }
        index = i;

        let fn = middleware[i];

        if (i === middleware.length) {
          fn = next;
        }

        if (!fn) {
          return Promise.resolve();
        }

        try {
          return Promise.resolve(fn(ctx, () => {
            return dispatch(i + 1);
          }));
        } catch (err) {
          return Promise.reject(err);
        }
      }
    };
  }
  ```
  使用该中间件引擎
  ```js
  let middleware = [];
  let context = {
    data: []
  };
  
  middleware.push(async(ctx, next) => {
    console.log('action 001');
    ctx.data.push(2);
    await next();
    console.log('action 006');
    ctx.data.push(5);
  });
  
  middleware.push(async(ctx, next) => {
    console.log('action 002');
    ctx.data.push(2);
    await next();
    console.log('action 005');
    ctx.data.push(5);
  });
  
  middleware.push(async(ctx, next) => {
    console.log('action 003');
    ctx.data.push(2);
    await next();
    console.log('action 004');
    ctx.data.push(5);
  });
  
  const fn = compose(middleware);
  
  fn(context)
    .then(() => {
      console.log('end');
      console.log('context = ', context);
    });
  
  // 结果显示
  // "action 001"
  // "action 002"
  // "action 003"
  // "action 004"
  // "action 005"
  // "action 006"
  // "end"
  // "context = { data: [1, 2, 3, 4, 5, 6]}"
  ```

## AOP面向切面编程
- 举例
  * 农场的水果包装流水线一开始只有`采摘 - 清洗 - 贴标签`
  * 为了提高销量，想加上两道工序`分类`和`包装`但又不能干扰原有的流程，同时如果没增加收益可以随时撤销新增工序
  * 最后在流水线的中的空隙插上两个工人去处理，形成`采摘 - 分类 - 清洗 - 包装 - 贴标签`的新流程，而且工人可以随时撤回
  * AOP即在程序生命周期或者横向流程中`加入/减去`一个或多个功能，不影响原有功能
  * 中间件机制即AOP
- 洋葱模型是切面的一种
  * 洋葱模式包括以下几个元素
    - 生命周期
    - 中间件
    - 中间件在生命周期中
      * 前置操作
      * 等待其他中间件操作
      * 后置操作
  
## 中间件分类
### 一般的中间件具备的功能
- HTTP服务
- 路由管理
- 模板渲染
- 日志
- 插件/中间件等AOP
- 其他
### Koa提供的两种能力
- HTTP服务
- 中间件机制(AOP切面)
### 狭义中间件
- 最简单的中间件
- 直接被`app.use()`
### 间接中间件(间接被`app.use()`加载)(类似react的`HOC`)
```js
const Koa = require('koa');
let app = new Koa();

function indirectMiddleware(path, middleware) {
  return async function(ctx, next) {
    console.log(ctx.path === path, middleware);
    if (ctx.path === path) {
      await middleware(ctx, next);
    } else {
      await next();
    }
  };
}

const index = async function(ctx, next) {
  ctx.body = 'this is index page';
};

const hello = async function(ctx, next) {
  ctx.body = 'this is hello page';
};

const world = async function(ctx, next) {
  ctx.body = 'this is world page';
};

app.use(indirectMiddleware('/', index));
app.use(indirectMiddleware('/hello', hello));
app.use(indirectMiddleware('/world', world));

app.listen(3001, () => {
  console.log('the demo is start at port 3001');
});
```
### 子中间件(其他方式接入Koa切面)
- 初始化中间件时，内置子中间件列表
- 子中间件列表添加子中间件元素
- 子中间件列表封装成间接中间件，让后被`app.use()`加载
- 例子
  ```js
  const Koa = require('koa');
  let app = new Koa();

  class Middleware{
    constructor() {
      this.stack = [];
    }

    use(path, childMiddleware) {
      this.stack.push({ path, middleware: childMiddleware })
    }

    middlewares() {
      let stack = this.stack;
      return async function(ctx, next) {
        let path = ctx.path;
        for( let i=0; i<stack.length; i++ ) {
          const child = stack[i];
          if( child && child.path === path && child.middleware ) {
            await child.middleware(ctx, next);
          }
        }
        await next();
      }
    }
  }

  const middleware = new Middleware();
  middleware.use('/page/001', async(ctx, next) => { ctx.body = 'page 001' })
  middleware.use('/page/002', async(ctx, next) => { ctx.body = 'page 002' })
  middleware.use('/page/003', async(ctx, next) => { ctx.body = 'page 003' })

  app.use(middleware.middlewares());

  app.listen(3001, function(){
    console.log('the demo is start at port 3001');
  })
  ```

## 中间件实现 —— koa-logger(狭义)(请求拦截)
- step01:拦截请求，打印请求URL
- step02:操作响应，打印响应URL
- 例子
  ```js
  const logger = async function(ctx, next) {
    let res = ctx.res;
    // 拦截操作请求 request
    console.log(`<-- ${ctx.method} ${ctx.url}`);
    await next();
    // 拦截操作响应 request
    res.on('finish', () => {
      console.log(`--> ${ctx.method} ${ctx.url}`);
    });
  };
  module.exports = logger
  ```
  ```js
  const Koa = require('koa');
  const logger = require('./index');
  const app = new Koa();
  app.use(logger);
  app.use(async(ctx, next) => {
    ctx.body = 'hello world';
  });
  app.listen(3000, () => {
    console.log('[demo] is starting at port 3000');
  });
  ```

## 中间件实现 —— koa-send(狭义)(请求拦截)
- 拦截请求，判断该请求是否请求本地静态资源文件
- 操作响应，返回对应的静态文件文本内容或出错提示
- step 01 配置静态资源绝对目录地址
- step 02 判断是否支持隐藏文件
- step 03 获取文件或者目录信息
- step 04 判断是否需要压缩
- step 05 设置HTTP头信息
- step 06 静态文件读取
- 源码
  ```js
  const fs = require('fs');
  const path = require('path');
  const {
    basename,
    extname
  } = path;
  
  const defaultOpts = {
    root: '',
    maxage: 0,
    immutable: false,
    extensions: false,
    hidden: false,
    brotli: false,
    gzip: false,
    setHeaders: () => {}
  };
  
  async function send(ctx, urlPath, opts = defaultOpts) {
    const { root, hidden, immutable, maxage, brotli, gzip, setHeaders } = opts;
    let filePath = urlPath;
  
    // step 01: normalize path
    // 配置静态资源绝对目录地址
    try {
      filePath = decodeURIComponent(filePath);
      // 禁止访问项目所处文件夹以外的文件夹
      if (/[\.]{2,}/ig.test(filePath)) {
        ctx.throw(403, 'Forbidden');
      }
    } catch (err) {
      ctx.throw(400, 'failed to decode');
    }
  
    filePath = path.join(root, urlPath);
    const fileBasename = basename(filePath);
  
    // step 02: check hidden file support
    // 判断是否支持隐藏文件
    if (hidden !== true && fileBasename.startsWith('.')) {
      ctx.throw(404, '404 Not Found');
      return;
    }
  
    // step 03: stat
    // 获取文件或者目录信息
    let stats; 
    try { 
      stats = fs.statSync(filePath);
      if (stats.isDirectory()) {
        ctx.throw(404, '404 Not Found');
      }
    } catch (err) {
      const notfound = ['ENOENT', 'ENAMETOOLONG', 'ENOTDIR']
      if (notfound.includes(err.code)) {
        ctx.throw(404, '404 Not Found');
        return;
      }
      err.status = 500
      throw err
    }
  
    let encodingExt = '';
    // step 04 check zip
    // 判断是否需要压缩
    if (ctx.acceptsEncodings('br', 'identity') === 'br' && brotli && (fs.existsSync(filePath + '.br'))) {
      filePath = filePath + '.br';
      ctx.set('Content-Encoding', 'br');
      ctx.res.removeHeader('Content-Length');
      encodingExt = '.br';
    } else if (ctx.acceptsEncodings('gzip', 'identity') === 'gzip' && gzip && (fs.existsSync(filePath + '.gz'))) {
      filePath = filePath + '.gz';
      ctx.set('Content-Encoding', 'gzip');
      ctx.res.removeHeader('Content-Length');
      encodingExt = '.gz';
    }
  
    // step 05 setHeaders
    // 设置HTTP头信息
    if (typeof setHeaders === 'function') {
      setHeaders(ctx.res, filePath, stats);
    }
  
    ctx.set('Content-Length', stats.size);
    if (!ctx.response.get('Last-Modified')) {
      ctx.set('Last-Modified', stats.mtime.toUTCString());
    }
    if (!ctx.response.get('Cache-Control')) {
      const directives = ['max-age=' + (maxage / 1000 | 0)];
      if (immutable) {
        directives.push('immutable');
      }
      ctx.set('Cache-Control', directives.join(','));
    }
  
    const ctxType = encodingExt !== '' ? extname(basename(filePath, encodingExt)) : extname(filePath);
    ctx.type = ctxType;
  
    // step 06 stream
    // 静态文件读取
    ctx.body = fs.createReadStream(filePath);
  }
  
  module.exports = send;
  ```
  使用
  ```js
  const send = require('./index');
  const Koa = require('koa');
  const app = new Koa();
  
  
  // public/ 为当前项目静态文件目录
  app.use(async ctx => {
    await send(ctx, ctx.path, { root: `${__dirname}/public` });
  });
  
  app.listen(3000);
  console.log('listening on port 3000');
  ```
## 中间件实现 —— koa-static(狭义)(请求拦截)
- Koa.js 官方对`koa-send`进行二次封装，推出了`koa-static`中间件，目标是用于做静态服务器或者项目静态资源管理
- 实现步骤
  * step01:配置静态资源绝对目录地址
  * step02:判断是否支持等待其他请求
  * step03:判断是否为 GET 和 HEAD 类型的请求
  * step04:通过`koa-send`中间件读取和返回静态文件
- 代码
  ```js
  const {resolve} = require('path');
  const send = require('./send');

  function statics(opts = {
    root: ''
  }) {
    opts.root = resolve(opts.root);

    // 是否需要等待其他请求
    if (opts.defer !== true) {
      // 如果需要等待其他请求
      return async function statics(ctx, next) {
        let done = false;

        if (ctx.method === 'HEAD' || ctx.method === 'GET') {
          try {
            await send(ctx, ctx.path, opts);
            done = true;
          } catch (err) {
            if (err.status !== 404) {
              throw err;
            }
          }
        }

        if (!done) {
          await next();
        }
      };
    } else {
      // 如果不需要等待其他请求
      return async function statics(ctx, next) {
        await next();

        if (ctx.method !== 'HEAD' && ctx.method !== 'GET') {
          return;
        }

        if (ctx.body != null || ctx.status !== 404) {
          return;
        }

        try {
          await send(ctx, ctx.path, opts);
        } catch (err) {
          if (err.status !== 404) {
            throw err;
          }
        }
      };
    }
  }

  module.exports = statics;
  ```
  使用
  ```js
  const path = require('path');
  const Koa = require('koa');
  const statics = require('./index');
  
  const app = new Koa();
  
  const root = path.join(__dirname, './public');
  app.use(statics({ root }));
  
  app.use(async(ctx, next) => {
    if (ctx.path === '/hello') {
      ctx.body = 'hello world';
    }
  });
  
  app.listen(3000);
  console.log('listening on port 3000');
  ```

## 参考
- [Gitbook-Koa设计模式](https://chenshenhai.github.io/koajs-design-note/)