# Entry与Plugin

## Entry
- 打包2次
  ```js
  module.exports = {
    entry: {
      main: "./src/index.js",
      sub: "./src/index.js"
    },
    ouput: {
      filename: "[name].js", // 占位符[name]即entry{}里的main以及sub
      path: path.resolve(__dirname, "dist")
    }
  }
  ```

## Output
- 一个重要的配置项：publicPath  
  它表示项目中那些如`import "./main.css"`这种用相对路径方式引入资源的url在打包后怎么修改其url  
  如：
  ```js
  output: {
    publicPath: "http://cdn.com.cn"
    filename: "bundle.js", // 打包后的文件名
    path: path.resolve(__dirname, "dist"), 
  }
  ```   
  那么`import "./main.css"`中的"main.css"会相对于`publicPath: "http://cdn.com.cn"`去查找资源  
  这一配置多用于CDN中
