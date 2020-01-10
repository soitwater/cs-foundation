# 把webpack配置文件放入build文件夹
以下假设`webpack.dev.js`以及`webpack.prod.js`以及`webpack.common.js`都放在项目根路径下的`build`文件夹里  
1. 在`webpackage.config.js`中  
   ```js
   output: {
     filename: "bundle.js", 
     path: path.resolve(__dirname, "../dist"), 
   }
   ```
2. `new CleanWebpackPlugin()`清空的位置其实是`output.path`