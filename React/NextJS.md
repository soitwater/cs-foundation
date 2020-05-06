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
- 或者使用脚手架工具搭建
  ```shell
  cnpm install -g create-next-app
  create-next-app next_demo
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
### 自带css
- 局部样式(仅在当前页面有效)
  ```js
  export default function () {
    return (
      <>
        <span className={'font'}>test4.js</span>
        <style jsx>
          {`
            .font {
              color: red
            }
          `}
        </style>
      </>
    )
  }
  ```
- 全局样式
  * `<style jsx>`是把样式添加到`html`的`<head>`中
  * 需要放在`_app.js`这种长期存在的组件,假如放在`page/test.js`这种短期组件(切换路由即卸载组件,卸载组件时`<style jsx global>`也会被卸载)
  * 代码
    ```js
    <style jsx global>
      {`
        .link {
          color: red;
        }
      `}
    </style>
    ```

### 使用`styled-component`进行服务端渲染
- `cnpm i -S styled-components babel-plugin-styled-components`
- 新建`.babelrc`
  ```js
  {
    "presets": ["next/babel"],
    "plugins": [
      [
        "import",
        {
          "libraryName": "antd"
        }
      ],
      ["styled-components", { "ssr": true }]
    ]
  }
  ```
- 在 `pages/_document.js` 里加入 `jsx` 的支持（覆写 `app` 的方法）
  ```js
  import Document, { Html, Head, Main, NextScript } from 'next/document'
  import { ServerStyleSheet } from 'styled-components'

  export default class MyDocument extends Document {
    static async getInitialProps(ctx) {
      const sheet = new ServerStyleSheet()
      // 劫持原本的renderPage函数并重写
      const originalRenderPage = ctx.renderPage

      try {
        ctx.renderPage = () =>
          originalRenderPage({
            // 根App组件
            enhanceApp: App => props => sheet.collectStyles(<App {...props} />),
          })
        // 如果重写了getInitialProps 就要把这段逻辑重新实现
        const props = await Document.getInitialProps(ctx)
        return {
          ...props,
          styles: (
            <>
              {props.styles}
              {sheet.getStyleElement()}
            </>
          ),
        }
      } finally {
        sheet.seal()
      }
    }

    // 如果要重写render 就必须按照这个结构来写
    render() {
      return (
        <Html>
          <Head />
          <body>
            <Main />
            <NextScript />
          </body>
        </Html>
      )
    }
  }
  ```
- 在页面`A.js`
  ```JS
  import styled from 'styled-components'

  const Title = styled.h1`
    color: yellow;
    font-size: 40px;
  `
  const A = ({ name }) => (
    <>
      <Title>这是A页面</Title>
    </>
  )

  export default A
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

## getInitialProps 在页面获取数据
- 相当于`props`,可获取页面中数据 or 全局`App`中的数据
- 同步客户端与服务端渲染的数据
- 这个方法在服务端渲染和客户端渲染都会执行（刷新 或 前端跳转)
- 如果服务端渲染已经执行过了，在进行客户端渲染时就不会再帮你执行了。
- 代码
  ```js
  let Test = function ({ router, name }) {
    return (
      <Button>test.js-->{name}</Button>
    )
  }
  // `getInitialProps`是一个静态方法，如果是用在`class`，则写作`static `
  Test.getInitialProps = (ctx) => {
    // 这里可以放ajax请求，去后端请求数据；拿到数据后传给本页面；之后页面才会渲染
    return {
      name: 'jocky'
    }
  }
  ```

## 自定义 _document.js
- 因为服务端渲染没有`index.html`(SPA中的页面模板)，因此`_document.js`相当于代替`index.html`
- 用于引入外部css文件,js文件等
- 修改`_document.js`需要重启服务
- 代码
  ```js
  import Document, { Html, Head, Main, NextScript } from 'next/document'

  export default class MyDocument extends Document {
    static async getInitialProps(ctx) {
      // 调用原 Document.js 的 getInitialProps()
      const props = await Document.getInitialProps(ctx);
      return {
        ...props
      };
    }

    render() {
      return (
        <Html>
          <Head>
            <style>{`.test{color:red;}`}</style>
          </Head>
          <body>
            <div>123</div>
            // 必须有 <Main /> <NextScript />
            // 若没有<NextScript />, 将移除外部js文件?
            <Main />
            <NextScript />
          </body>
        </Html>
      )
    }
  }
  ```

## 自定义 _app.js
- 每一页面都需要渲染这一组件`_app.js`
- 用处：`固定layout`,`保持公用状态redux`,`给所有页面传入自定义数据`,`自定义错误处理`
- 代码
  ```js
  import App, { Container } from 'next/app'

  class MyApp extends App {
    // 调用组件的`getInitialProps`方法
    static async getInitialProps({ Component, ctx }) {
      let pageProps
      console.log('>>', Component)
      if (Component.getInitialProps) {
        pageProps = await Component.getInitialProps(ctx)
      }
      console.log('--', pageProps)
      return { pageProps }
    }
    // 重写 render 方法
    render() {
      const { Component, pageProps } = this.props
      return (
        <Container>
          <Component {...pageProps}></Component>
        </Container>
      )
    }
  }

  export default MyApp
  ```

## 自定义layout
- 新建`components/Layout.js`
- 代码
  ```js
  const layoutStyle = { margin: 20, padding: 20, border: '1px solid #DDD' }

  export default ({ children }) => {
    return (
      <div style={layoutStyle}>
        {children}
      </div>
    )
  }
  ```
- 在需要使用模板的地方
  ```js
  import Layout from '../components/Layout.js'

  export default () => (
    <Layout>
      <h1>Hello Next.js</h1>
    </Layout>
  )
  ```

## title
- `NextJS`会合并多个`<Head>`
- `import Head from 'next/head';`
- 代码
  ```js
  <Head>
    <title>index</title>
    <meta name="viewport" content="initial-scale=1.0, width=device-width" />
  </Head>
  ```


## 路由
- `pages`文件夹下的一个文件，对应一个路由
### 路由跳转
- 使用`Link`或者`Router`进行跳转
  ```js
  // 跳转方法1
  // <Link></Link>内部是唯一节点
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
- 如果真的想显示成`/b/2`这种形式的话， 也可以通过`Link`上的`as`属性来实现(路由映射)
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


## 异步加载
- 页面都是异步加载的,js文件也是默认异步加载的。但是假如一个js模块在多个页面都用到，则会被提取到打包后的公共的`vendor.js`里。
- 解决方式是`webpack`的动态`import`方法
  ```js
  A.getInitialProps = async ctx => {
    const moment = await import('moment')
    const timeDiff = moment.default(Date.now() - 60 * 1000).fromNow()
    return { timeDiff }
  }
  ```
- 异步加载组件
  * next 官方为我们提供了一个`dynamic`方法
  * 使用这种方式引入普通的 react 组件，这个组件的代码就只会在 A 页面进入后才会被下载
  * 代码
    ```js
    import dynamic from 'next/dynamic'
    const Comp = dynamic(import('../components/Comp'))
    const A = ({ name, timeDiff }) => {
      return (
        <><Comp /></>
      )
    }
    export default A
    ```

## next.config.js的配置
```js
const withCss = require('@zeit/next-css')

const configs = {
  // 输出目录
  distDir: 'dest',
  // 是否每个路由生成Etag
  generateEtags: true,
  // 本地开发时对页面内容的缓存
  onDemandEntries: {
    // 内容在内存中缓存的时长(ms)
    maxInactiveAge: 25 * 1000,
    // 同时缓存的页面数
    pagesBufferLength: 2,
  },
  // 在pages目录下会被当做页面解析的后缀
  pageExtensions: ['jsx', 'js'],
  // 配置buildId
  generateBuildId: async () => {
    if (process.env.YOUR_BUILD_ID) {
      return process.env.YOUR_BUILD_ID
    }

    // 返回null默认的 unique id
    return null
  },
  // 手动修改webpack配置
  webpack(config, options) {
    return config
  },
  // 手动修改webpackDevMiddleware配置
  webpackDevMiddleware(config) {
    return config
  },
  // 可以在页面上通过process.env.customkey 获取 value
  env: {
    customkey: 'value',
  },
  // 下面两个要通过 'next/config' 来读取
  // 可以在页面上通过引入 import getConfig from 'next/config'来读取

  // 只有在服务端渲染时才会获取的配置
  serverRuntimeConfig: {
    mySecret: 'secret',
    secondSecret: process.env.SECOND_SECRET,
  },
  // 在服务端渲染和客户端渲染都可获取的配置
  publicRuntimeConfig: {
    staticFolder: '/static',
  },
}

if (typeof require !== 'undefined') {
  require.extensions['.css'] = file => {}
}

// withCss得到的是一个nextjs的config配置
module.exports = withCss(configs)
```

## 服务端渲染liuc
- 浏览器请求：
  * 浏览器发起`/page`请求
  * Koa接收到请求，并调用`Next.js`
  * `Next.js`开始渲染
  * 调用APP的`getInitialProps`
  * 调用页面的`getInitialProps`
  * 渲染最终`html`
  * 返回给浏览器渲染
- 客户端路由跳转
  * 点击链接按钮
  * 异步加载页面的组件js
  * 调用页面的`getInitialProps`
  * 数据返回，路由变化
  * 渲染新页面
- 服务端生成的数据在文件夹`.next`,渲染后返回页面的数据如下
  ```js
  <script id="__NEXT_DATA__" type="application/json">
    {
      "dataManager":"[]",
      "props":
        {
          "pageProps":{"timeDiff":"a minute ago"}
        },
      "page":"/a",
      "query":{},
      "buildId":"development",
      "dynamicBuildId":false,
      "dynamicIds":["./components/Comp.jsx"]
    }
  </script>
  ```

## 参考
- [nextJS搭建环境](https://cloud.tencent.com/developer/article/1612742)