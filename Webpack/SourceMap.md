# SourceMap
- 在`mode: 'development'`下，默认`SourceMap`是启动的，可以添加`devtool: "none"`关闭`SourceMap`
- SourceMap若关闭,当项目报错时,debug时看的是打包后的js代码(看都看不懂)
- SourceMap是一种映射关系，当打包后的代码出错时，它能告诉开发者其实是源码的哪一行错误，更直观
- 启动SourceMap:`devtool: "source-map"`
- `devtool: "inline-source-map"`表示将映射关系`.map`文件打包到`bundle.js`文件(output文件)中，而非`devtool: "source-map"`的独立文件
- `devtool: "cheap-inline-source-map"`,提升打包性能(出错时,只提示错误的行数,而不精确到列数; 且只管理业务代码的错误,不管理引入的第三方模块的错误)
- `devtool: "eval"`,打包速度最快的，使用`eval`建立映射关系，但提示的错误信息较少

## 推荐配置
- `mode: 'development'`  
  `devtool: "cheap-module-eval-source-map"`,推荐,兼顾打包速度以及代码错误提示
- `mode: 'production'`  
  `devtool: "cheap-module-source-map"`,线上环境一般不用配置`devtool`，硬要配置就用这一条

## SourceMap原理
- JavaScript脚本正变得越来越复杂。大部分源码（尤其是各种函数库和框架）都要经过转换，才能投入生产环境。
  * 常见的源码转换，主要是以下三种情况：
    - 压缩，减小体积。比如jQuery 1.9的源码，压缩前是252KB，压缩后是32KB。
    - 多个文件合并，减少HTTP请求数。
    - 其他语言编译成JavaScript。最常见的例子就是CoffeeScript。
  * 这三种情况，都使得实际运行的代码不同于开发代码，除错（debug）变得困难重重。
- Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置


## 参考
- [中文文档](https://www.webpackjs.com/configuration/devtool/)
- [JavaScript Source Map 详解](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)