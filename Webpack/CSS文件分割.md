# CSS文件分割

## CSS in JS
webpack打包css时，会把css放入js文件

## MiniCssExtractPlugin
- 安装  
  `npm install --save-dev mini-css-extract-plugin`
- 引入  
  在webpack.prod.js（最好在生产环境下，因为该插件还不支持热更新）引入`const MiniCssExtractPlugin = require('mini-css-extract-plugin')`
- 配置
  ```js
  // 在webpack.prod.js中
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');
  const prodConfig = {
    plugins: [
      new MiniCssExtractPlugin({
        filename: '[name].css',
      }),
    ]
  }
  ```
- 处理loader
  ```js
  // 剪切webpack.common.js里的相应css-loader
  {
    test: /\.styl$/,
    use: [
      "style-loader", 
      {
        loader: "css-loader",
        options: {
          importLoaders: 2, // 有时css文件里还引用其他子css文件，这些子css文件也需要被"stylus-loader"以"postcss-loader"处理
        }
      }, 
      "postcss-loader", 
      "stylus-loader"
    ], // 因为需要多个loader，因此use的类型不是对象，而是数组
  }, 

  // 在webpack.dev.js(保持原来的loader)
  module: {
    rules: [
      {
        test: /\.styl$/,
        use: ["style-loader", 
          {
            loader: "css-loader",
            options: {
              importLoaders: 2, // 有时css文件里还引用其他子css文件，这些子css文件也需要被"stylus-loader"以及"postcss-loader"处理
            }
          }, 
          "postcss-loader", 
          "stylus-loader"
        ], // 因为需要多个loader，因此use的类型不是对象，而是数组
      }, 
    ]
  }

  // 而webpack.prod.js需要一点变更
  module: {
    rules: [
      {
        test: /\.styl$/,
        use: [
          MiniCssExtractPlugin.loader, // 变更位置在这里 所有的style-loader 都替换为 MiniCssExtractPlugin.loader
          {
            loader: "css-loader",
            options: {
              importLoaders: 2, 
            }
          }, 
          "postcss-loader", 
          "stylus-loader"
        ], 
      }, 
    ]
  }
  ```
- 运行`npm run build`
- 然而, 还是没有`.css`文件出现...原因：  
  ```js
  // 检查optimization，避免摇树
  optimization: {
    usedExports: true
  }

  // 之后在package.json中看 "sideEffects": ... 将css文件写入(一个关键点)
  {
    "sideEffects": [
      "*.css"
    ]
  }
  ```

## 配置MiniCssExtractPlugin插件
文件命名
```js
plugins: [
  new MiniCssExtractPlugin({
    filename: '[name].css', // 主入口文件的命名方式
    chunkFilename: '[name].chunk.css'// 非项目引入，而是在主入口文件中被引入的文件的命名方式
  }),
],
```
css文件压缩(合并,css代码写在一行)
```js
// 安装
cnpm i -D optimize-css-assets-webpack-plugin

// 引入
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')

// 配置
optimization: {
  minimizer: [new OptimizeCSSAssetsPlugin({})],
}
```
该插件也支持cacheGroups以及splitChunks
```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          enforce: true,
        },
      },
    },
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
}
```


## 参考
- [MiniCssExtractPlugin文档](https://webpack.js.org/plugins/mini-css-extract-plugin/#root)