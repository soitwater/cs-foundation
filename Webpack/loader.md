# loader
这里测试用的index.html是自己创建，而非webpack创建的

## 例子
webpack默认不知道怎么打包jpg文件，需要在配置文件下添加规则  
```js
module: {
  rules: [{
    test: /\.jpg$/,
    use: {
      loader: "file-loader"
    }
  }]
},
```
其中file-loader需要另外安装`npm install file-loader -D`  
file-loader的操作过程大约是：
- 将非JS资源复制到dist文件夹下(假设是dist文件夹)
- 然后返回其文件路径
- 写法
  ```js
  import avatar from "./a.jpg" // 更推荐ES6模块语法,虽然浏览器不支持,但webpack会通过打包解决这一问题的
  let img = new Image() // 创建Img标签
  img.src = avatar
  document.getElementById("root").append(img) // 在id为root的div下插入图片
  ```
- 图片会被打包成很长的字符串，如果想要保持原名称需要额外配置`options`  
  ```js
  module: {
    rules: [{
      test: /\.(jpg|png|gif)$/,
      use: {
        loader: "file-loader",
        options: {
          name: "[name].[ext]", // [name]表示原文件的name，[ext]表示原文件的后缀，更多占位符看文档
          outputPath: "images/", // 将图片打包到dist下的images文件夹
        },
      }
    }]
  },
  ```

## url-loader
- `url-loader`可以实现`file-loader`的所有功能，且支持`file-loader`没有的其他功能  
- 注意不同的是：file-loader会复制图片到dist(假设)文件夹下，而url-loader是将其转化为`base64`字符串  
  `base64`可以减少HTTP请求，但假如图片很大，那`base64`内容也将很大，页面加载时间增大  
  故小图片用`base64`,大图片用`file-loader`  
  ```js
  module: {
    rules: [{
      test: /\.jpg$/,
      use: {
        loader: "url-loader",
        options: {
          name: "[name].[ext]",
          outputPath: "images/",
          limit: 2048 // 小于2kb则使用url-loader转化为`base64`, 超出则使用`file-loader`
        },
      }
    }]
  },
  ```

## 样式
- 通常打包样式需要两个loader,在`webpack.config.js`里的module下的rules数组里插入
```js
{
  test: /\.css$/,
  use: ["style-loader", "css-loader"], // 因为需要多个loader，因此use的类型不是对象，而是数组
}
```
- css-loader: 将多个css文件按联系打包到一起
- style-loader: 将css.loader的内容挂载到页面的`<head>`部分  
- 使用stylus  
```js
{
  test: /\.css$/,
  use: ["style-loader", "css-loader", "stylus-loader"], // 因为需要多个loader，因此use的类型不是对象，而是数组
}
```
<br/>

- 自动添加浏览器前缀
  * 命令行安装`stylus`以及`postcss-loader`以及`autoprefixer`
  * 在webpack.config.js的同级目录下创建`postcss.config.js`
  * 在`postcss.config.js`写入  
    ```js
    module.exports = {
      plugins: [
        require('autoprefixer')
      ]
    }
    ```
  * 在`webpack.config.js`中写入
    ```js
    {
      test: /\.styl$/,
      use: ["style-loader", "css-loader", "postcss-loader", "stylus-loader"], // 注意书写顺序
    }
    ```
- 特殊情况(子css文件也接收loader处理)
  ```js
  {
    test: /\.styl$/,
    use: ["style-loader", 
      {
        loader: "css-loader",
        options: {
          importLoaders: 2, // 有时css文件里还引用其他子css文件，这些子css文件也需要"stylus-loader"以及"postcss-loader"处理
        }
      }, 
      "postcss-loader", 
      "stylus-loader"
    ], 
  }
  ```

## CSS Module
即scoped，该css只在当前模块内有效，避免全局css属性之间冲突、覆盖。写法：  
  ```js
  {
    test: /\.styl$/,
    use: ["style-loader", 
      {
        loader: "css-loader",
        options: {
          importLoaders: 2, 
          modules: true, // 开启css模块化
        }
      }, 
      "postcss-loader", 
      "stylus-loader"
    ], 
  }
  ```
  在项目文件里，引入css不再是
  ```js
  import "./avatar.css"
  ```  
  而是(不同文件里的css都是独立的)  
  ```js
  import style1 from "./svstar.css" 
  // 使用, img是img标签
  img.classList.add(style1.avatar)
  ```

## 打包iconfont
使用file-loader即可打包`svg  ttf  eot`

## loader顺序
loader顺序是反序（从下到上，从右到左）

## 参考
- [正文文档file-loader](https://www.webpackjs.com/loaders/file-loader/)