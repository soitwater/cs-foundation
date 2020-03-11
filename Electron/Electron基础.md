# Electron基础

## 安装
- `cnpm i -g electron-prebuilt`
- `electron -v`显示版本
- `git clone https://github.com/electron/electron-quick-start`
- `cd electron-quick-start`
- `npm i && npm start`

## 常用开发模块
### 安装`cnpm i -D electron-is-dev` 判断当前是否开发环境
* 在`main.js`
  ```js
  const isDev = require('electron-is-dev')
  const urlLocation = isDev ? 'http://localhost:3000' : 'dummyurl'
  mainWindow.loadURL(urlLocation)
  ```
### Electron防止频繁重启
- 用cli方式开发时，修改程序入口文件（如main.js）时，常常需要重新`npm start`
  * 可以安装`nodemon`,帮助完成重启项目
  * 再修改`package.json`
    ```json
    "scripts": {
      "start": "npx nodemon --watch main.js --exec \"electron .\""
    }
    ```
### Electron启动时不自动打开浏览器
- 启动时不自动打开浏览器
  * cnpm i -D cross-env
  * 在`package.json`
    ```json
    {
      "scripts": {
        "dev": "npx nodemon --watch main.js --exec \"electron .\" | cross-env BROWSER=none npm start"
      }
    }
    ```
## React 中使用 Electron
- `create-react-app`创建应用`my-preject`
- `cd my-preject`
- `npm i `
- `npm i electron -S`
- 修改`package.json`
  ```json
  "main": "main.js",
  "scripts": {
    "dev":"electron . --debug | npm start" // 同时运行 react 以及 electron 两个项目
  }
  ```
- 在`package.json`同级下新建`main.js`
  ```js
  const { app, BrowserWindow } = require('electron')
  const path = require('path')
  let mainWindow
  function createWindow() {
    mainWindow = new BrowserWindow({
      width: 800,
      height: 600,
      webPreferences: {
        preload: path.join(__dirname, 'preload.js')
      }
    })
    // 载入页面 - 与 React 的地址保持一致
    mainWindow.loadURL('http://localhost:3000')
    mainWindow.on('closed', function () {
      mainWindow = null
    })
  }
  app.on('ready', createWindow)
  app.on('window-all-closed', function () {
    if (process.platform !== 'darwin') app.quit()
  })
  app.on('activate', function () {
    if (mainWindow === null) createWindow()
  })
  ```

## 主进程
- 即与`package.json`同级的`main.js`
- `main.js`文件有且仅有1个

## Electron与React的项目下使用NodeJS常用模块的配置项




## 参考
—— [electron官方文档](https://electronjs.org/)