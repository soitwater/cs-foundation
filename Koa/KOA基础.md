# KOA基础

## 创建项目
- npm init -y
- `cnpm i koa koa-static http-proxy-middleware async koa-router koa-bodyparser koa-json -D`
  * koa-static 静态资源
  * http-proxy-middleware 用于代理
  * async 异步操作
  * koa-router 路由控制
  * koa-bodyparser 解析出请求体中的POST数据
  * koa-json GET数据提交
- 其他常用的koa工具,如`koa-generator`(用于快速搭建`koa项目`)
- 新建`/api.js`
- 设置cookie
  ```js
  const Koa = require('koa');
  let app = new Koa();
  
  app.use(async ctx => {
    if (ctx.url === '/index') {
      ctx.cookies.set('username', 'lisa', {
        domain: 'localhost',
        path: '/index',   //cookie写入的路径
        maxAge: 1000 * 60 * 60 * 1,
        expires: new Date('2018-07-06'),
        httpOnly: false,
        overwrite: false
      })
      ctx.body = 'cookies is seted;'
    } else {
      console.log(ctx.cookies.get('username'))
      if (ctx.cookies.get('username')) {
        ctx.body = 'welcome ' + ctx.cookies.get('username');
      } else {
        ctx.body = 'hello koa2'
      }
    }
  })
  
  app.listen(3333, () => {
    console.log('set cookie')
  })
  ```

## 访问静态资源
```js
const Koa = require('koa');
const path = require('path');
const static = require('koa-static');
const app = new Koa();

app.use(static(
  path.join(__dirname, 'public'),{    //静态文件所在目录
    maxage: 30*24*60*60*1000        //指定静态资源在浏览器中的缓存时间
  }
));

// 前端访问（不需要写 public） http://localhost:3333/资源
```

## GET请求
- 第一种
  ```js
  ctx.url.indexOf('/api/homeList.json?page=') >= 0) {
    console.log(ctx.query) // { page: '1' }
  }
  ```
- 第二种
  ```js
  ctx.url.indexOf('/api/homeList.json/:page') >= 0) {
    console.log(ctx.query)
  }
  ```

## 中间件
- 即洋葱模型：一根针扎入洋葱,从外到内;拔出洋葱,从内到外

## 路由 koa-router
```js
var Koa = require('koa');
var Router = require('koa-router');

var app = new Koa();
var router = new Router();

router.get('/', (ctx, next) => {
  // ... ...
});

app
  .use(router.routes())
  .use(router.allowedMethods());
```

## 参考
- [文档](https://koa.bootcss.com/)