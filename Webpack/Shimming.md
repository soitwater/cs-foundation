# Shimming
对webpack不足之处/默认行为的补足

## 方法
因为webpack是按模块打包的，因此模块1与模块2之间非耦合  
假如模块2需要使用模块1变量，还需要`import "模块2"`  
但假如模块1是一个通用库，在数十个业务模块也都希望使用它，但一个个配置过于麻烦，因此可以借助webpack进行配置  
- 在`webpack.common,js`  
  ```js
  {
    plugins: [
      new webpack.ProvidePlugin({
        $: "jquery"
      })
    ]
  }
  ```
  那么webpack在打包模块时，如果遇到`$`符号，则自动`import $ from "jquery"`

## imports-loader
默认每个模块的`this`都指向自身,若想`this`指向`window`，需借助插件