# TS如何引入JS库
- 把js库翻译成ts库
- 页面引入 + 全局定义
  * 缺点是全局定义的变量只能是`any`类型
  ```html
  <html>
  <head>
    <script type="text/javascript" src="js/jquery.min.js"></script>
  </head>
  <body>
  ```
  ```js
  declare var $;
  class App {
    init(){
      $("#msg").html("jquery引用成功!");
    }
  }
  ```
- 写类型定义文件`*.d.ts`


## 参考
- [纯TypeScript开发Web前端（二）如何引入JS库](https://www.cnblogs.com/eedc/p/12166326.html)