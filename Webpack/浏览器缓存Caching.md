# 浏览器缓存Caching
根据哈希值让浏览器请求最新js文件  
若Hash值不变,浏览器会使用缓存  
- 在`webpack.prod.js`中
  ```js
  {
    output: {
      filename: "[name].[contenthash].js",
      chunkfilename: "[name].[contenthash].chunk.js", // [contenthash]js文件内容变了该Hash值也会变
    }
  }
  ```
- 其他   
  老版本的webpack(webpack4之前)，即便没修改js代码，contenthash也会发生改变，原因是mainfest，解决方法是：
  ```js
  {
    optimization: {
      runtimeChunk: {
        name: "runtime"
      }
    }
  }
  ```