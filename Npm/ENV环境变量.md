# ENV环境变量
- 一般一个项目都会有以下 3 种环境：
  * 开发环境（开发阶段，本地开发版本，一般会使用一些调试工具或额外的辅助功能）
  * 测试环境（测试阶段，上线前版本，除了一些 bug 的修复，基本不会和上线版本有很大差别）
  * 生产环境（上线阶段，正式对外发布的版本，一般会进行优化，关掉错误报告）
- 我们可以在根目录下创建以下形式的文件进行不同环境下变量的配置：
  ```
  .env                # 在所有的环境中被载入
  .env.local          # 在所有的环境中被载入，但会被 git 忽略
  .env.[mode]         # 只在指定的模式中被载入
  .env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
  ```
- 内容以键值对的形式呈现
  ```
  NODE_ENV=stage
  VUE_APP_TITLE=stage mode
  ```
  使用 `process.env.[name]` 进行访问
- 改变环境`mode`的方法
  ```
  "scripts": {
    "serve": "vue-cli-service serve --mode stage",
  }
  ```
  * `--mode stage` 其实就是修改了 webpack 4 中的 mode 配置项为 stage
  * 同时其会读取对应 .env.\[model\] 文件下的配置
  * 如果没找到对应配置文件，其会使用默认环境 development
- 配置项权重
  ```
  .env.[mode].local > .env.[mode] > .env.local > .env 
  ```
 * 存在多个配置文件时，相同配置项权重大的覆盖小的，不同配置项它们会进行合并操作，类似于js中的`Object.assign`的用法。
- webpack 通过`DefinePlugin`内置插件将`process.env`注入到客户端代码中，故我们才能在配置文件中访问`.env`环境变量
  ```js
  // webpack 配置
  {
    plugins: [
      new webpack.DefinePlugin({
        'process.env': {
          NODE_ENV: JSON.stringify(process.env.NODE_ENV)
        }
      }),
    ],
  }

  ```