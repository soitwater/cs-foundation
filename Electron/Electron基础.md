# Electron基础

## 安装
- `cnpm i -g electron-prebuilt`
- `electron -v`显示版本
- `git clone https://github.com/electron/electron-quick-start`
- `cd electron-quick-start`
- `npm i && npm start`
- 生成识别码的库'uuid'(即随机码)
  * 常用使用：随机生成-v4版本
  * 根据时间戳生成-v1版本

## 安装模块过程中的错误`failed to install correctly`
* 前往`https://npm.taobao.org/mirrors/electron/`找到最新稳定版的版本号
* 假如是windows平台,那么下载`electron-v?.?.?-win32-x64.zip`
* 然后在`node_modules\electron\`下创建`dist`文件夹
* 将压缩包的内容解压到`dist`中
* 在`node_modules\electron\`中创建`path.txt`,内容为`electron.exe`(对应自己的平台,不同平台不一样)


## 常用开发模块
### 统一环境变量写法
- 安装`cnpm i -D cross-env`
  * 修改`package.json`下的`scripts`下的`dev`为：
    - `"dev": "electron . --debug | cross-env BROWSER=none npm start"`
    - 查看该环境变量`process.env.BROWSER === 'none'`
  * 统一跨平台的应用的环境变量的格式为`NODE_ENV=production`,(因为windows不支持这种格式)
  * (cross-env)能够提供一个设置环境变量的scripts,让你能够以unix方式(`NODE_ENV=production`)设置环境变量,然后在windows上也能兼容运行
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
- 使`react项目 + electron项目`下的渲染进程支持`nodejs`模块(在浏览器中依然报错)
  * 方案一
    - 在`index.html`
      ```html
      <head>
        <script>
          window.electron = require('electron');
        </script>
      </head>
      ```
    - 在`main.js`
      ```js
      mainWindow = new BrowserWindow({
        webPreferences: {
          nodeIntegration: true, // 是否集成Nodejs
        }
      })
      ```
    - 在渲染进程
      ```js
      const fs = window.require('fs') 
      // 还有诸如：window.electron.ipcRenderer 
      ```
  * 方案二
    - 在`非main.js`主进程的位置引入`nodejs`模块
      * `const fs = window.require('fs')`
  
## 主进程
- 即与`package.json`同级的`main.js`
- `main.js`文件有且仅有1个

## Electron与React的项目下使用NodeJS常用模块的配置项


## SQL
- Electron使用Sqlite3需要很多配置,不如使用`lowdb`

## electron 关闭跨域限制
```js
// main.js 主线程入口文件
mainWindow = new BrowserWindow({
  webPreferences: {webSecurity: false},
})
```

## 通信
- ipc通信(渲染进程与主进程的通信)
  * 在`main.js`（主进程不需做特别的变化，仍然是像在`NodeJS`中一样使用）
    ```js
    const { ipcMain } = require('electron')
    ipcMain.on('message', (avt, args) => { console.log(evt, args) })
    ```
  * 在react组件中
    ```js
    const ipcRenderer = window.electron.ipcRenderer
    <Button onClick={() => { ipcRenderer.send('message', 'ipc: test to main.js') }}>ipc</Button>
    ```
- remote(更简单的跨进程通信方法)
  * 在react组件中
    ```js
    const { BrowserWindow } = require('electron').remote
    let win = new BrowserWindow({width: 800, height: 600})
    win.loadURL("https://baidu.com")
    ```
## 用户配置信息
* Electron的用户配置信息不能放置在应用下,否则应用升级将覆盖配置信息
* 在`渲染进程`下使用`electron.remote.app.getPath('userData')`获取用户配置信息路径
* 在`main进程`下使用`electron.app.getPath('userData')`获取用户配置信息路径

## 打包
* 先打包react的代码
  - 之后确保代码仅靠react打包后代码以及`main.js`文件即可运行
  - 将`main.js`以及`react打包后的文件`以及`package.json`放进另一个文件夹
  - `package.json`下的`devDependencies`以及`dependencies`清空
    * `devDependencies`下新增`electron`等包
    * 重新安装依赖,准备开始打包
    * [参考](https://www.baidu.com/link?url=BLH8PEu4Z07zgtFQCwjta3DjMEcMM75WV8EsqQdmp0wa1C22BT0uDXyxiovH46pKfkj6OkWy2oc3zD0Tk6drG_&wd=&eqid=bf31851d00182e95000000065e1485a2)
* `cnpm i electron-builder -g`
* 在`package.json`中加入：
  - [配置解析](https://segmentfault.com/a/1190000016695922)
  ```json
  {
    "build": {
      "productName": "electron-file-management",
      "appId": "com.soitwater.management",
      "copyright": "BSD",
      "directories": {
        "output": "./build"
      },
      "win": {
        "target": [
          "nsis",
          "zip"
        ],
        "icon": "public/icon.ico"
      },
      "nsis": {
        "oneClick": false,
        "allowElevation": true,
        "allowToChangeInstallationDirectory": true,
        "installerIcon": "./build/icons/icon.ico",
        "uninstallerIcon": "./build/icons/icon.ico",
        "installerHeaderIcon": "./build/icons/icon.ico"
      }
    },
    "scripts": {
      "dist": "electron-builder --win --x64"
    }
  }
  ```
* 运行`npm run dist`,会提示它开始下载依赖包(速度慢,最好自己下载)
  - 到国内镜像网站`https://npm.taobao.org/mirrors/electron/7.1.7/`下载,将文件放到下面的位置(假设是`7.1.7`版本)
  - 存放位置
    ```js
    Linux: $XDG_CACHE_HOME or ~/.cache/electron/
    MacOS: ~/Library/Caches/electron/
    Windows: $LOCALAPPDATA/electron/Cache or ~/AppData/Local/electron/Cache/
    ```
* icon
  - 在需要一张`256*256`的图片,不能用`png`改后缀,如`icon.ico`
  - 把上面的图片也放一张到`/build/icons/`下，(build是打包导出的路径)
    * [在线制作icon](http://ico.duduxuexi.com/)
    * [参考](http://www.freesion.com/article/9563137056/)
    * [参考2](https://www.cnblogs.com/e0yu/p/11316964.html)
  - 一旦安装失败,出现错误`The process cannot access the file because it is being used by another process.`
    * 需要删除`导出位置下`除了`icons`的所有文件
* 防止Github被墙
  - 在`C:\Windows\System32\drivers\etc\hosts`添加`54.231.98.184 github-com.s3.amazonaws.com`
* 打包安装后程序空白
  - 不是`mainWindow.loadURL(url)`,而是`mainWindow.loadFile('index.html')`
* 解压`.zip`包或者点击`setup`,都能进行安装

## 参考
—— [electron官方文档](https://electronjs.org/)