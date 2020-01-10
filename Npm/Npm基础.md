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


## 参考
- [npm install --save 和 npm install -d的区别](https://blog.csdn.net/wangguoyu1996/article/details/80443861)