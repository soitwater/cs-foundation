# Npm发布

##  package.json设置
```json
{
  "name": "@soitwater/myui", // 重要--@soitwater表示私人仓库 发布时使用: npm publish --access=public 表示公开,避免付费
  "version": "0.1.0", // 重要 -- 每次上传时,版本号都需要比之前更大
  "private": false, // 重要 -- 避免付费
  "main": "./dist/mvui.common.js", // 重要--入口文件
  "files": [ // 重要--表示上传哪些文件
    "dist/*",
    "doc/*",
    "examples/*",
    "packages/*",
    "public/*",
    "tests/*",
    "*.json",
    "*.js",
    "*.md"
  ],
  "scripts": {
    "build-bundle": "vue-cli-service build --target lib --name mvui ./packages/index.js", // 重要--在npm publish前执行
  },
  "dependencies": {
    "vue": "^2.6.10",
  },
  "devDependencies": {
    "@babel/plugin-transform-runtime": "^7.5.0",
  }
}
```

## 问题
`no_perms Private mode enable, only admin can publish this module`  
- 出现原因：使用的是淘宝源cnpm,登陆到的是cnpm  
- 解决方法：切换到npmjs的网址，代码如下
- `npm config set registry http://registry.npmjs.org/`  

<br/>  

`operation not permitted, unlink 'c:\Temp\npm-20936-b98f84c8\tmp\fromDir-02dd5394\package.tgz'`  
- 发布前注意检查是否已经登录`npm`账号：`npm whoami`
- 登录:`npm login`

## 参考
- [【npm】利用npm安装/删除/发布/更新/撤销发布包](https://www.cnblogs.com/penghuwan/p/6973702.html)