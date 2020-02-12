# CssModules
css modules是scoped CSS的替代方案。虽然其实差不多，但socped CSS没有修改类名,只是添加了自定义属性`data-v-hash值`。而css modules则更加彻底，它会直接修改类名。

## 写法
- 在style上
  ```css
  <style module>
    .red {color: red;}
  </style>
  ```
- 在template上
  ```html
  <template>
    <p :class="$style.red">
      This should be red
    </p>
  </template>
  
  <!--如果类名中包含中划线,则使用中括号的写法-->
  <h4 :class="$style['header-tit']">类别推荐</h4>
  
  <!--也可以使用数组的语法-->
  <p :class="[$style.red, $style.bold]">
    Red and bold
  </p>
  ```
- 配置  
  css-loader关于CSS modules的默认配置如下 
  ```js
  {
    modules: true,
    importLoaders: 1,
    localIdentName: '[hash:base64]'
  }
  ```
  可以使用vue-loader的cssModules选项为css-loader进行自定义的配置
  ```js
  module: {
    rules: [
      {
        test: '\.vue$',
        loader: 'vue-loader',
        options: {
          cssModules: {
            localIdentName: '[path][name]---[local]---[hash:base64:5]',
            camelCase: true
          }
        }
      }
    ]
  }
  ```