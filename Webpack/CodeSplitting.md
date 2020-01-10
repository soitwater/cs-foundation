# CodeSplitting

## 为什么需要分块
- 假如业务代码不分块，有20MB，那么打包后单个JS文件体积巨大，加载时间长
- 部分代码是固定不变的，比如外部依赖库，假如用户刷新，将重新加载全部JS文件，没必要


## 如何分块
- 简单分块  
  外部库与业务代码分开，并分别打包
  ```js
  module.exports = {
    entry: {
      main: "./src/index.js",
      lodash: "./src/lodash.js",
    }
  }
  ```
- CodeSplitting  
  * 库与业务代码可以写在一起
  * 在`webpack.config.js`中添加  
    ```js
    {
      optimization: {
        splitChunks: {
          chunks: "all"
        }
      }  
    }
    ```
    webpack会自动进行代码分割，不管是同步导入的库还是异步导入的库:
    但需在webpack.common.js中做`optimization`的配置

## 示例
- 异步组件进行代码`dynamic-import`
  在index.js
  ```js
  function getComponent () {
    return import (/* webpackChunkName:'lodash' */"lodash").then(({ default: _ }) => {
      let el = document.createElement("div")
      el.innerHTML = _.join(["Xiao", "Ming"], "-")
      return el
    })
  }
  
  getComponent().then(el => {
    document.body.appendChild(el)
  })
  ```
  安装()  
  `npm i -D @babel/plugin-syntax-dynamic-import`  
  在`.babelrc`下配置
  ```js
  { // 最外层花括号
    "plugins": ["@babel/plugin-syntax-dynamic-import"]
  }
  ```
  确保webpack.common.js下的output  
  ```js
  output: {
    filename: "[name].js", // 打包后的文件名
    path: path.resolve(__dirname, "../dist"), // 打包后文件(bundle.js)的存放路径，必须是绝对路径
  },
  optimization: {
    splitChunks: {
      chunks: "all", 
      cacheGroups: {
        vendors: false, // 避免打包后的js文件有前缀vendor
        default: false,
      }
    }
  },
  ```
- 备注同步引入外部库
  ```js
  import _ from "lodash"
  doSomething()
  ```

## SplitChunksPlugin
webpack的代码分割功能底层是使用了`SplitChunksPlugin`插件  
```js
// 该插件的默认配置
splitChunks: {
  chunks: "async", // 最对异步代码进行分割, 可改成`all`或'initial', 配合下面的`cacheGroups`进行同步代码分割
  minSize: 30000, // 当js文件大于30000KB，才进行代码分割 
  maxSize: 50000, // 打包后的js文件若大于50000，则尝试进行二次代码分割(若可行,但一般不行，故此属性一般不配置)，每份代码最多不大于50000
  minChunks: 1, // 被引用次数 ≥ 1时，才进行代码分割 
  maxAsyncRequests: 5, // 假如引用了10个类库，则将代码分割为10份，那么将导致页面请求同时加载10份js文件，为此maxAsyncRequests表示当webpack打包了5份js文件后，将不再进行代码分割（一般默认）
  maxInitialRequests: 3, // 外部库也会引入其他文件，这部分代码最多打包3次后将不再进行打包（一般默认）
  automaticNameDelimiter: '~', // 文件前缀与文件名之间的连接符
  name: true, // （一般默认）
  cacheGroups: { // 缓存组，若打包后的文件符合其内部的组要求(test正则)，则先缓存，不进一步打包，同步引入的代码会进入这里
    vendors: {
      test: /[\\/]node_modules[\\/]/, // 是否以同步方式引入的外部库是在`node_modules`下, 若是则属于vendors组
      priority: -10, // 一份打包后的js文件应该进入哪一组先看“test”,之后再看“priority”，“priority”值越大越匹配
      filename: "vendors", // 令所有vendors组的外部库都塞进一个名为vendors.js的文件中
    },
    default: {
      minChunks: 2,
      priority: -20,
      reuseExistingChunk: true, // 如果某一模块已经被打包，被再次引用时，那么不必再次打包，直接复用之前的被打包的文件
      filename: "common", // 让default组的js文件都合成common.js
    }
  }
}
```

## 参考
- [文档](https://www.webpackjs.com/plugins/split-chunks-plugin/)