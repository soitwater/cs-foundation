# babel
## 作用
- 将JavaScript 中`es2015/2016/2017/2018`的新语法转化为`es5`
- babel的最新版是`7.x`(2019年底)

## 使用方式
- 使用`单文件`
- 命令行`cli`
- 作为webpack的插件`babel-loader`

## babel运行机制
- 三个阶段：解析、转换、生成
- babel本身不具备转化功能,它将转化的功能分解到一个个 plugin 里面。因此当我们不配置任何插件时，经过 babel 的代码和输入是相同的
- babel有2种插件：语法插件(即让babel识别某一新语法) + 转译插件(如令babel支持将ES2018语法转换至ES2015)
- 使用
  * 根目录下创建`.babelrc`或者`package.json`的`babel`里面，格式相同
  * 使用`npm install babel-plugin-xxx`进行安装
- 一个`babel`项配置
  ```json
  "babel": {
    "presets": [
      ["env", {
        "targets": {
          "browsers": ["last 2 versions", "safari >= 7"],
          "node": "current" // NodeJS的最新的稳定版本支持的特性不会被`babel`转化
        }
      }]
      "react-app"
    ],
    "plugins": [
      [
        "import",
        {
          "libraryName": "antd",
          "style": "css"
        }
      ]
    ]
  },
  ```
  * presets
    - 假如每次使用`babel`时，都需要用户手动配置需要的几十个插件，将很麻烦。
    - 为此babel预设置了一些插件套餐，用户可直接选择插件套餐。
    - 其值可能为
      * 官方内容：`env | react | flow`等
      * stag-x(已经删除)：`Stage 0 | Stage 1 | Stage 2 | Stage 3 | Stage 4`,依次表示`想法|提案|初稿|成为规范+浏览器实现|明年标准`
      * 已经不推荐：`es201x | latest`,都被`env`取代
    - `presets`的加载顺序从后往前（保证向后兼容）（用户书写需要按时间顺序列出）

## 配套工具
- @babel/cli
  * 安装为`devDependencies`
  * 写在`package.json`的`scripts`下
- @babel/node
  * 它是`babel-cli`的一部分
  * `babel-node = babel-polyfill + babel-register`
- @babel/polyfill
  * 它应该是安装为`dependencies`
  * 其内部集成了`core-js`和`regenerator`
    - `core-js`:转换一些内置类 (`Promise`, `Symbols`等等) 和静态方法 (`Array.from`等),自动引入
    - `regenerator`: 作为`core-js`的拾遗补漏，主要是`generator/yield`和`async/await`两组的支持。当代码中有使用`generators/async`时自动引入
  * 它会污染全局变量,若是开发轮子,应谨慎使用
- @babel/register
  * 改写`require`命令
  * 它是对`require`进来的文件进行转译,而不是当前所在文件
  * 它是实时转译,仅用于开发环境
- @babel/runtime 和 @babel/plugin-transform-runtime
  * `babel-runtime`其内部集成了`core-js`和`regenerator`
  * `babel-plugin-transform-runtime`依赖于`babel-runtime`,它能减小转译后代码的体积
- @babel/loader
  * 与`babel-cli`功能类似,但它在webpack等构建工具中使用
    ```js
    module: {
      rules: [
        {
          test: /\.js$/,
          exclude: /(node_modules|bower_components)/,
          use: {
            loader: 'babel-loader',
            options: {}
          }
        }
      ]
    }
    ```

## 举例 
- 在`index.js`文件中写点ES2015语法
- 打包不能使用`webpack-dev-server`(放在内存中看不到)，应使用`webpack`(生成本地文件容易看)
- 不用`babel`webpack会把ES6代码原封不动用`eval()`执行，在新浏览器中没问题，但在ES5浏览器会报错

## 特点
* 简单转换(只能转化一些语法糖吧,但像`new Promise()`是做不到的)  
  - 安装  
    `npm install --save-dev babel-loader @babel/core`
  - `webpack.config.js`中添加规则  
    ```js
    module: {
      rules: [
        { 
          test: /\.js$/, 
          exclude: /node_modules/, 
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"]
          } 
        }
      ]
    }
    ```
  - 安装  
    `npm install @babel/preset-env --save-dev`
* Polyfill
  - 安装  
    `npm install --save @babel/polyfill`  
  - 复制`import "@babel/polyfill";`到文件夹`src/index.js`项目文件的最顶层  
    这种方法不好，会把没用到的Polyfill也打包进js文件，造成js文件巨大
  - `webpack.config.js`中添加规则  
    ```js
    module: {
      rules: [
        { 
          test: /\.js$/, 
          exclude: /node_modules/, 
          loader: "babel-loader",
          options: {
            presets: [["@babel/preset-env", {
              useBuiltIns: "usage" // 只添加需要的pollyfill
            }]]
          } 
        }
      ]
    }
    ```

## 打包UI组件库时的配置
- 一般的babel配置是全局配置的，但在打包UI库时不希望污染全局变量，因此需要`plugin-transform-runtime`(避免pollyfill污染全局变量)：
- 安装  
  `npm install --save-dev @babel/plugin-transform-runtime`  
  `npm install --save @babel/runtime`  
  `npm install --save @babel/runtime-corejs2`
- webpack.config.js配置
  ```js
  // 删除preset改为：
  module: {
    rules: [
      { 
        test: /\.js$/, 
        exclude: /node_modules/, 
        loader: "babel-loader",
        options: {
          "plugins": [
            [
              "@babel/plugin-transform-runtime",
              {
                "absoluteRuntime": false,
                "corejs": 2, // 重要
                "helpers": true,
                "regenerator": true,
                "useESModules": false
              }
            ]
          ]
        } 
      }
    ]
  }
  ```

## babel配置过长
- 在项目目录下创建.babelrc
- ```js
  // 将webpack.config.js下的module.rules里的babel-loader的options删除
  // 在.babelrc里添加
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "absoluteRuntime": false,
        "corejs": 2,
        "helpers": true,
        "regenerator": true,
        "useESModules": false
      }
    ]
  ]
  ```

## 令NodeJS支持ES-Module
- `cnpm i @babel/core @babel/preset-env`
- 根目录下创建`.babelrc`，并输入
  ```json
  {
    "presets": [
      ["@babel/preset-env", {
        "targets": {
          "node": "current"
        }
      }]
    ]
  }
  ``` 

## 参考
- [babel官网](https://www.babeljs.cn/)
- [安装教程](https://www.babeljs.cn/setup#installation)
- [polyfill](https://www.babeljs.cn/docs/babel-polyfill)
- [transform-runtime](https://www.babeljs.cn/docs/babel-plugin-transform-runtime)
- [一口（很长的）气了解 babel](https://zhuanlan.zhihu.com/p/43249121)