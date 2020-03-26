# nvm

- 控制当前电脑的NodeJS的版本（有时电脑不同项目的NodeJS版本要求不同，nvm可以帮助快速切换版本）
- 安装
  * 卸载原始安装的NodeJS
  * 在`https://github.com/coreybutler/nvm-windows/releases`下载`nvm-setup.zip`
  * 检查安装程序是否自动配置了环境变量（一般默认自动配置环境）
  * `nvm -v`
  * 安装NodeJS:`nvm install 6.9.0`
- 用法
  ```js
  node -v
  // 切换版本
  nvm use v8.9.4
  ```