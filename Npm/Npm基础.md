# Npm基础
1. --save 与 --save-dev区别  
   npm install -D 就是npm install --save-dev  
   npm insatll -S 就是npm install --save  
2. 淘宝镜像  
   `npm install -g cnpm --registry=https://registry.npm.taobao.org`  
   恢复镜像设置  
   `npm config set registry https://registry.npmjs.org`  
3. 查看源
   `npm config get registry`
4. 本次从淘宝仓库源下载
   `npm --registry=https://registry.npm.taobao.org install`   
5. 设置淘宝源
   `npm config set registry https://registry.npm.taobao.org`
6. 设置为默认源
   `npm config set registry http://registry.npmjs.org/`
7. 快速修改Nodejs全局包的放置位置
   - `npm config ls`
   - `npm config set prefix E:/路径`

## 区别
* npm与cnpm的区别
 - cnpm装的各种node_module，这种方式下所有的包都是扁平化的安装。node_modules下展开后有非常多的文件，导致了在打包的过程中非常慢。
 - 如果改用npm来安装node_modules的话，所有的包都是树状结构的，层级变深。
 - 由于这个不同，对一些项目比较大的应用，很容易出现打包过程慢且node内存溢出的问题（使用npm安装包,可以加速`electron`打包）。
* devDependencies与dependencies的区别
 - dependencies 表示我们要在生产环境下使用该依赖，devDependencies 则表示我们仅在开发环境使用该依赖。
 - 我自己的说法是,在自己写的代码中,用到了`import`或者`require`,就应该放到`dependencies`(`electron`除外)

## NPX
1. npx nodemon --version 查找局部安装的模块的版本

## package.lock
- 若没有`package.lock`, 则项目的依赖一直跟随最新版本,可能导致开发/生产环境的依赖不同(bug)
- 建议项目带上`package.lock`


## 技巧
```js
// 查看某依赖是否已安装
npm info xxx
```

## 参考
- [npm install --save 和 npm install -d的区别](https://blog.csdn.net/wangguoyu1996/article/details/80443861)