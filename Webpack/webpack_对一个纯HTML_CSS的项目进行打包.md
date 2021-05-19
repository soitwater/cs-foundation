# 对一个纯HTML_CSS的项目进行打包

## 预处理
```js
// 创建项目
npm init --yes
// 安装webpack及其cli
npm i webpack webpack-cli --save-dev
// 需要的loader
npm install css-loader --save-dev 
npm install style-loader --save-dev
npm install url-loader --save-dev
npm install html-webpack-plugin --save-dev
npm install copy-webpack-plugin --save-dev
```

## 文件结构
### package.json
```json
// 注意 html-webpack-plugin 与 webpack & webpack-cli 的版本应对应
{
  "name": "webpack_demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "copy-webpack-plugin": "^8.1.1",
    "css-loader": "^5.2.4",
    "html-webpack-plugin": "^5.3.1",
    "style-loader": "^2.0.0",
    "url-loader": "^4.1.1",
    "webpack": "^5.37.0",
    "webpack-cli": "^4.7.0"
  }
}
```

### index.js
- 这是一个纯HTML、CSS的静态网站，所以没有相关的js逻辑，但webpack打包入口通常是js文件，因此这里是以一个只引入`CSS`文件的`index.js`文件作为入口。
  ```js
  // index.js
  import "./css/common.css";
  import "./css/index.css";

  console.log('载入');
  ```
- webpack.config.js
  ```js
  const path = require("path");
  const HtmlWebpackPlugin = require("html-webpack-plugin");

  module.exports = {
    mode: "development",
    entry: "./index.js",
    output: {
      filename: "main.js",
      path: path.join(__dirname, "dist"),
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: ["style-loader", "css-loader"],
        },
        // 处理字体文件，这四个是fonts目录下四个文件的后缀名
        {
          test: /\.(woff|ttf|svg|eot)$/,
          use: ["url-loader"],
        },
      ],
    },
    plugins: [
      // 令js文件自动注入html中
      new HtmlWebpackPlugin({
        template: "./html/index.html",
      }),
    ],
  };
  ```

### 运行
- 执行`npx webpack`




