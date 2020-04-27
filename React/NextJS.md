# NextJS

## 创建
- `npm init -y`
- `cnpm i -S next react react-dom `
- creat document创建文件夹 `pages`
  * new file 新建文件`pages/index.js`
    ```js
    export default function () {
      return (<span>index.js</span>)
    }
    ```
- 新增`package.json`中的`scripts`
  ```json
  {
    "script": {
      "dev":"next",
      "build":"next build",
      "start":"next start"
    }
  }
  ```

## 集成依赖
### 集成KOA
- 安装
  ```shell
  cnpm i koa koa-static http-proxy-middleware async koa-router koa-bodyparser koa-json -D
  ```
- 修改`package.json`的`"dev": "node server.js"`
- 新增文件`server.js`
  ```js
  const Koa = require('koa')
  const Router = require('koa-router')
  const next = require('next')
  
  const dev = process.env.NODE_ENV !== 'production'
  const app = next({ dev })
  const handle = app.getRequestHandler()
  
  const PORT = 3001
  // 等到pages目录编译完成后启动服务响应请求
  app.prepare().then(() => {
    const server = new Koa()
    const router = new Router()
  
    server.use(async (ctx, next) => {
      await handle(ctx.req, ctx.res)
      ctx.respond = false
    })
  
    server.listen(PORT, () => {
      console.log(`koa server listening on ${PORT}`)
    })
  })
  ```
### 引入静态资源
- 根目录下新建文件夹叫`static`。代码可以通过`/static/`来引入相关的静态资源。
  ```js
  export default () => <img src="/static/my-image.png" alt="my image" />
  ```

### 集成css (原始NextJS不识别`.css`后缀文件)
- `cnpm i -S @zeit/next-css`
- 新建`next.config.js`
  ```js
  const withCss = require('@zeit/next-css')

  if (typeof require !== 'undefined') {
    require.extensions['.css'] = file => {}
  }
  // withCss得到的是一个next的config配置
  module.exports = withCss({})
  ```
- 其他语言类似
  ```java
  @zeit/next-css
  @zeit/next-sass
  @zeit/next-less
  @zeit/next-stylus
  ```

### 集成antd
- 安装`cnpm i -S antd -D babel-plugin-import`(按需引入)
- 新建`pages/_app.js`(NextJS提供的重写`App`组件的方式)
  ```js
  import App from 'next/app'
  import 'antd/dist/antd.css'
  export default App
  ```
- `touch .babelrc`(把`import { Button } from 'antd'`解析成`import Button from 'antd/lib/button')`
  ```js
  {
    "presets": ["next/babel"],
    "plugins": [
      [
        "import",
        {
          "libraryName": "antd"
        }
      ]
    ]
  }
  ```

## 路由
- `pages`文件夹下的一个文件，对应一个路由
### 路由跳转
- 使用`Link`或者`Router`进行跳转
  ```js
  // 跳转方法1
  import Link from 'next/link'
  // 跳转方法2
  import Router from 'next/router'
  import { Button } from 'antd'

  export default () => {
    const goB = () => {
      Router.push('/b')
    }

    return (
      <>
        <Link href="/a">
          <Button>跳转到a页面</Button>
        </Link>

        <Button onClick={goB}>跳转到b页面</Button>
      </>
    )
  }
  ```
### 动态路由
-  `next`中，只能通过`query`来实现动态路由，不支持`/b/:id`这样的定义方法
  ```js
  import Router from 'next/router'
  import { Button } from 'antd'

  export default () => {
    const goB = () => {
      Router.push('/b?id=2')
      // 或
      Router.push({
        pathname: '/b',
        query: {
          id: 2,
        },
      })
    }

    return <Button onClick={goB}>跳转到b页面</Button>
  }
  ```
- 假如跳转路径是`/b?id=2`,那么`跳转至`的页面这么写
  ```js
  import { withRouter } from 'next/router'

  const B = ({ router }) => <span>这是B页面, 参数是{router.query.id}</span>
  export default withRouter(B)
  ```
### 动态路由2
- 如果真的想显示成`/b/2`这种形式的话， 也可以通过`Link`上的`as`属性来实现
  ```html
  <Link href="/a?id=1" as="/a/1">
    <Button>跳转到a页面</Button>
  </Link>
  ```
  或者
  ```js
  Router.push(
    {
      pathname: '/b',
      query: {
        id: 2,
      },
    },
    '/b/2'
  )
  ```
- 但是上述的方法将导致刷新后`404`，因为这里的路由是前端修改的，刷新后服务端并不认识此路由
  * 解决方法是
    ```js
    const Koa = require('koa')
    const Router = require('koa-router')
    const next = require('next')

    const dev = process.env.NODE_ENV !== 'production'
    const app = next({ dev })
    const handle = app.getRequestHandler()

    const PORT = 3001
    // 等到pages目录编译完成后启动服务响应请求
    app.prepare().then(() => {
      const server = new Koa()
      const router = new Router()

      // start
      // 利用koa-router去把/a/1这种格式的路由
      // 代理到/a?id=1去，这样就不会404了
      router.get('/a/:id', async ctx => {
        const id = ctx.params.id
        await handle(ctx.req, ctx.res, {
          pathname: '/a',
          query: {
            id,
          },
        })
        ctx.respond = false
      })
      server.use(router.routes())
      // end

      server.use(async (ctx, next) => {
        await handle(ctx.req, ctx.res)
        ctx.respond = false
      })

      server.listen(PORT, () => {
        console.log(`koa server listening on ${PORT}`)
      })
    })
    ```
### 路由钩子
- 先后触发：`routeChangeStart; beforeHistoryChange; routeChangeComplete`
- 报错触发: `routeChangeError`
- 监听方式：`Router.events.on(eventName, callback)`


## 参考
- [nextJS搭建环境](https://cloud.tencent.com/developer/article/1612742)