# Stylus

## 使用
在Vue-Cli3中使用stylus，直接查看`参考`下的官方文档的配置方法即可
```js
npm install -D stylus-loader stylus
// 在*.vue文件下直接使用即可
<style lang="scss">
  $color: red;
</style> 
// 假如需要自动化导入文件 (用于颜色、变量、mixin……)，可以使用 style-resources-loader
npm i style-resources-loader -D
// 在vue.config.js中, 以下方法更推荐
module.exports = {
  css: {
    loaderOptions: {
      stylus: {
        data: `@import "~@/assets/styles/imports.styl";`
      }
    }
  },
}
// 其中 @import “~@” 是语法糖
chainWebpack: (config)=>{
  config.resolve.alias.set('@', resolve('src'))
},
// 在vue组件中引入外部*.styl文件的时候
@import "~@/assets/styles/imports.styl"
不能写作 @import url("../") 
```

## 语法
- 选择器
  * stylus支持无花括号(使用缩进表示)，无分号，无冒号的写法
  * 可省略父级选择器，父级选择器可用`&`代替
- 变量  
  * `bgColor = red`
  * 属性查找
    ```css
    width: w = 150px
    height: h = 80px
    margin-left: -(w / 2)
    margin-top: -(h / 2)

    /*或者*/

    width: 150px
    height: 80px
    margin-left: -(@width / 2)
    margin-top: -(@height / 2)
    ```
- 方法
  ```css
  add(a, b)
    a + b
  body 
    padding add(10px, 5)
  ```
- Mixins  
  与方法类似,但无返回值,应用也不同, Mixins主要用于代码的复用
  ```css
  border-radius(n)
  -webkit-border-radius n
  -moz-border-radius n
  border-radius n

  form input[type=button]
    border-radius(5px)
  ```
- 条件
  ```css
  overload-padding = true

  if overload-padding
    padding(y, x)
      margin y x
  body
    padding 5px 10px
  ```
- @extend 继承
  ```css
  .message {
    padding: 10px;
    border: 1px solid #eee;
  }
  
  .warning {
    @extend .message;
    color: #E2E21E;
  }

  /*等同于*/
  .message, .warning {
    padding: 10px;
    border: 1px solid #eee;
  }
  .warning {
    color: #E2E21E;
  }
  ```


## 参考
- [Stylus-NodeJS下构建更富表现力/动态/健壮的CSS](https://www.zhangxinxu.com/wordpress/2012/06/stylus-nodejs-expressive-dynamic-robust-css/)
- [文档](https://www.zhangxinxu.com/jq/stylus/)
- [Vue-Cli3官方配置文档](https://cli.vuejs.org/zh/guide/css.html#%E5%BC%95%E7%94%A8%E9%9D%99%E6%80%81%E8%B5%84%E6%BA%90)
- [『前端干货篇』：你不知道的Stylus](https://juejin.im/post/5bbd7a7c6fb9a05cfd27f4c6)