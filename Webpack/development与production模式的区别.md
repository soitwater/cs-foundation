# development与production模式的区别
- 新建两个文件`webpack.dev.js`(开发环境配置)以及`webpack.prod.js`(生产环境配置)
- 在package.json下的命令
  ```json
  {
    "scripts": {
      "dev": "webpack-dev-server --config webpack.dev.js", // 使用开发环境的webpack配置进行打包
      "build": "webpack --config webpack.prod.js", // 使用生产环境的webpack配置进行打包
    }
  }
  ```

## 配置区别
配置项|webpack.prod.js|webpack.dev.js
-|-|-
mode|production|development
devtool|cheap-module-source-map|cheap-module-eval-source-map
devServer|不需要|需要
HMR|不需要|需要
optimization|不需要|需要

## `webpack.dev.js`与`webpack.prod.js`间存在大量相同配置
- 创建`webpack.common.js`
- 剪切共用代码放入`webpack.common.js`
  ```js
  module.exports = {
    entry: {

    },
    module: {

    },
    plugins: [], // 取出2个文件的共有插件
    output: {

    },
  }
  ```
- 合并
  * 安装  
    `npm i webpack-merge -D`  
  * `webpack.dev.js`以及`webpack.prod.js`都引入merge及common配置
    ```js
    const merge = require("webpack-merge")
    const commonConfig = require("./webpack.common.js")
    ```
    将原来的`module.exports`改为`const devConfig / prodConfig`
  * 导出  
    两个文件都是在最后一行添加  
    `module.exports = merge(commonConfig, devConfig)`或者  
    `module.exports = merge(commonConfig, prodConfig)`或者  
