# webpack配置

Vue3.0需在根目录下新建vue.config.js来配置项目，支持webpack-chain链式api调用  
参考:[官方文档](https://cli.vuejs.org/zh/config/#publicpath)

- 配置一
```js
const path = require('path');
function resolve (dir) {
  return path.join(__dirname, dir)
}
module.exports = {
  publicPath: "/", // 默认，表示项目部署在根目录，如：https://www.my-app.com/
  publicPath: "/my-app/", // 默认，表示项目部署在根目录下的"/my-app/"，如：https://www.my-app.com/my-app/
  publicPath: "" 或者 "./", // 表示相对路径,但在使用上有一些限制(为什么？)

  outputDir: "dist", // 默认值，即生产环境构建文件的目录(每一次构建都会清空"dist", 传入 --no-clean 可关闭该行为)
  assetsDir: "", // 默认值, 放置生成的静态资源 (js、css、img、fonts) 的 (相对于 outputDir 的) 目录
  indexPath: "index.html", // 指定生成的 index.html 的输出路径 (相对于 outputDir)
  filenameHashing: true, // 默认值, 生成的静态资源在它们的文件名中包含了 hash 以便更好的控制缓存

  pages: undefined, // 默认值,看官方文件,用于多页面应用模式

  lintOnSave: true, // 默认值,将eslint-loadder的错误变成警告(在命令行中输出)
  lintOnSave: false, // 关闭 eslint
  devServer: {
    overlay: { // 下面的设置,让浏览器也显示 eslint 警告/错误
      warnings: true,
      errors: true
    },
    proxy: "http://localhost:4000", // 开发环境下的服务器地址
    proxy: { // 配置跨域
      '/api': {
　　　  //要访问的跨域的api的域名
        target: 'http://localhost:5001/api/',
        ws: true,
        changOrigin: true,
        pathRewrite: {
          '^/api': ''
        }
      }
    },
  },

  runtimeCompiler: false, // 默认值, 是否使用包含运行时编译器的 Vue 构建版本(决定能否在 Vue 组件中使用 template 选项)
  transpileDependencies: [], // 默认值, babel-loader 会忽略所有 node_modules 中的文件。
  transpileDependencies: [], // 若想要Babel显式转译一个依赖，可以在这个选项中列出
  productionSourceMap: true, // 默认，决定生产环境是否有 source map
  
  crossorigin: "anonymous" | "use-credentials",  // 少用,在<script>以及<link rel="stylesheet">上添加 crossorigin="anonymous"
  crossorigin: "anonymous",  // crossorigin="anonymous"请求时不携带cookie等认证信息
  crossorigin: "use-credentials",  // crossorigin="use-credentials"请求时携带cookie等认证信息
  crossorigin: "anonymous" | "use-credentials",  // 设置crossorigin时都要求response的header中带上Access-Control-Allow-Credentials属性
  
  integrity: false, // 略,与CDN相关,如果构建后的文件是部署在 CDN 上的，启用该选项可以提供额外的安全性

  configureWebpack: Object | Function, // 用于在 vue.config.js 中写 webpack 语法, 值可为Object | Function
  // 若为对象,和原 webpack 语法一致
  module.exports = {
    configureWebpack: {
      plugins: [
        new MyAwesomeWebpackPlugin()
      ]
    }
  },
  // 若为函数,则可基于环境有条件地进行配置
  module.exports = {
    configureWebpack: config => {
      if (process.env.NODE_ENV === 'production') {
        // 为生产环境修改配置...
      } else {
        // 为开发环境修改配置...
      }
    }
  },

  chainWebpack: config => { // 值为函数,允许对webpack配置进行更细粒度的修改, 使用了 webpack-chain 库,
    config.resolve.alias // 参考: https://segmentfault.com/a/1190000017547171?utm_source=tag-newest
      .set('@$', resolve('src')) // 参考: vue官网
  },

  module.exports = { // css 这一部分的规则被从 chainWebpack 中单独剥离
    css: {
      modules: true, // 若为false,则 CSS Module 仅针对后缀为 *.module.[css|styl|scss|sass|less]
      loaderOptions: { // 在这里表示向 css-loader 传递选项
        css: {
          // 给 预处理器 loader 传递选项
          sass: {
            // @/ 是 src/ 的别名
            // 所以这里假设你有 `src/variables.scss` 这个文件
            data: `@import "~@/variables.scss";`
          }

          // 
        },
      },
      extract: false, // 生产环境下默认是 true(css提取到独立的css文件)，开发环境下默认是 false(css动态注入到JS的inline代码)
      extract: false, // 若是开发UI库,可设置为false,避免用户再次导入css文件
    }
    // 参考: https://cli.vuejs.org/zh/guide/css.html#postcss
    // 其他功能: 即便未设置依然默认支持(Sass/Less/Stylus), 默认添加浏览器前缀
  }

  pluginOptions: { // 第三方插件设置的位置
    'style-resources-loader': {//https://github.com/yenshih/style-resources-loader
      preProcessor: 'scss',//声明类型
      'patterns': [
      ],
    }
  }

}
```


- 配置-关闭eslint
```js
// 方式1 - 修改 package.json 下的 eslint 配置
"eslintConfig": {
  "root": true,////此项是用来告诉eslint找当前配置文件不能往父级查找
  "env": {
    "node": true//此项指定环境的全局变量，下面的配置指定为node环境
  },
  "extends": [// 此项是用来配置vue.js风格，就是说写代码的时候要规范的写，如果你使用vs-code我觉得应该可以避免出错
    "plugin:vue/essential",
    "@vue/standard"
  ],
  "rules": {//规则配置写在这里
    "indent": [1, 4]
  },
  "parserOptions": {
    "parser": "babel-eslint"//此项是用来指定eslint解析器的，解析器必须符合规则，babel-eslint解析器是对babel解器包装使其与ESLint解析
  }
},

// 方式2 - 在 vue.config.js 中配置
devServer: {
  overlay: {
    warnings: false,
    errors: false
  },
}
```

- 配置 Babel  
新建`babel.config.js`进行配置,不在推荐用`.babelrc` 或 `package.json` 中的 `babel` 字段进行配置

- 配置ESLint  
ESLint 可以通过 `.eslintrc` 或 `package.json` 中的 `eslintConfig` 字段来配置。

- 配置 TypeScript  
通过 `tsconfig.json` 来配置

- 配置测试  
(单元测试以及E2E测试)略

## 其他概念
- `process.argv`  
  * [参考](http://nodejs.cn/api/process.html#process_process_argv)  
  * process.argv[0] 第一个参数,实际为NodeJS的运行程序的启动文件的位置
  * process.argv0 第一个参数的原始值
- `process.env`
  * process.env 属性返回包含用户环境的对象
- Vue-cli3的环境变量
  * [参考](https://cli.vuejs.org/zh/guide/mode-and-env.html)
  * 环境变量的存放位置    
    - .env                # 在所有的环境中被载入  
    - .env.local          # 在所有的环境中被载入，但会被 git 忽略  
    - .env.[mode]         # 只在指定的模式中被载入  
    - .env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略  
  * 环境变量可在命令中重新声明(不使用默认的环境)
    - `vue-cli-service build --mode `
- `process.env.NODE_ENV`
  * 在vue-cli3中,process.env.NODE_ENV是vue默认给的：
    - development 模式用于 vue-cli-service serve
    - production 模式用于 vue-cli-service build 和 vue-cli-service test:e2e
    - test 模式用于 vue-cli-service test:unit
- `VUE_APP_XXX`
  * 在客户端的使用方法：process.env.VUE_APP_XXX（存疑）

## 参考
- [vue-cli3的eslint配置问题](https://www.jianshu.com/p/bfc7e7329cff)