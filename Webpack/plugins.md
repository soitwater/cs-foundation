# plugins
plugins可以在webpack运行到某个时刻时，帮你做一些事情，类似于Vue的生命周期  

## 引用插件的方法
```js
// 安装
npm install --save-dev html-webpack-plugin
// webpack.config.js
var HtmlWebpackPlugin = require('html-webpack-plugin');
var path = require('path');

var webpackConfig = {
  entry: 'index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'index_bundle.js'
  },
  plugins: [new HtmlWebpackPlugin()]
}
// 所生产的html文件
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script>
  </body>
</html>
```

## html-webpack-plugin
- 该插件负责自动生成一个html文件，并在html文件中将打包后的其他文件(js，样式，图片等)引入该html中  
- 默认情况下的`html-webpack-plugin`生成的html文件是没有DOM元素的，假如需要可以在`new HtmlWebpackPlugin()`中进行配置：  
- 现在`src`文件夹下创建`index.html`，它是`HtmlWebpackPlugin`创建html文件的模板
  ```js
  plugins: [new HtmlWebpackPlugin({
    template: 'src/index.html' // 使用这一模板
  })]
  ```
- 写html模板时,如果在webpack配置文件中已经说明的js文件入口,入口js文件里也声明了其他的依赖的js文件，那么不需要在html模板里再次引入`<script src="./other.js"></script>`什么的，因为webpack会自动帮你插入。

## clean-webpack-plugin
- 安装`cnpm i -D clean-webpack-plugin`  
- 在webpack.config.js中引入`const {CleanWebpackPlugin} = require("clean-webpack-plugin")`
- 使用
  ```js
  // 在webpack.config.js
  module.export = {
    plugins: [new CleanWebpackPlugin()], // 这是新版的写法
  }
  ```
  
## loader 与 plugins 的区别
- loader 用于加载某些浏览器无法加载的资源文件(将无法加载的数据格式转化为可加载的数据格式)
  * 因为 webpack 只能理解 JavaScript 和 JSON 文件，对于其他资源例如 css，图片，或者其他的语法集，比如 jsx 是没有办法加载的。 
  * 例如：图片, typescript, stylus 等
- plugin 用于扩展webpack的功能(即 loader 无法完成的功能)
  * 从打包优化和压缩，到重新定义环境变量

## 参考
- [中文文档](https://www.webpackjs.com/plugins/html-webpack-plugin/)