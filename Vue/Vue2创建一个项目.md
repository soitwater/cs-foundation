# Vue-Cli创建一个项目
- Vue-Cli创建一个项目后需要做的事情

## 创建
- 在win10下，为了使Git Bash支持上下箭头，不用 `vue create 项目名称`而是使用`winpty vue.cmd create 项目名称`  
  * 推荐可以使用`vue ui`可视化的构建项目  
  * [vue-cli 3.0 UI控制台初体验](https://segmentfault.com/a/1190000015366009)
  * ## vue 创建项目报错   
    - `command failed: npm install --loglevel error --registry=https://registry.npm.taobao.org --disturl=https://npm.taobao.org/dist`
    - 找到文件`C:\Users\CSY\.vuerc`,找到属性`useTaobaoRegistry`其值修改为`true`
    - `npm cache clean --force` 清除npm的缓存
    - 升级 vue-cli / vue / npm / nodejs :   
      * `npm install -g @vue/cli`
      * `cnpm i vue -g`
      * n 是 nodejs 管理版本的一个特定的模块（n模块仅在 Linux 系统适用）
        - `npm install -g  --force n`
        - 稳定版 ：`n stable` 
        - 最新版`n latest`
        - 直接 n + 版本号 `12.13.0`
        - windows 升级 nodejs
          ```
          第一步：打开cmd查看之前的node版本安装的路径： where node
          第二步：直接去官网下载你想要的版本，安装在上述路径中覆盖即可。
          第三步： node -v 发现node版本更新到你想要的版本。
          ```
      * `npm install -g npm`
  
## 一些面试时可以谈论的点
- 清晰的文件目录
- 路由懒加载
- 开发环境下只编译自己关心的模块,保证`Hot reload`效率
- 打包分析,便于定位大模块(像vue这种应该放进CDN,而不应该放在webpack里进行打包)
  ```js
  // vue.config.js
  const BundleAnalyzerPlugin = require("webpack-bundle-analyzer").BundleAnalyzerPlugin;
  module.exports = {
    configureWebpack: {
      plugins: [
        new BundleAnalyzerPlugin()
        // 其他 plugins ...
      ]
    }
  };
  ```
- 匹配开发、测试、生产不同环境
- 调用方式一致，请求代码流程清晰
- 跨域支持
  
- 请求错误处理一致
  ```js
  // 使用 axios 同一处理
  // 前后端同一 http code
  ```

## public 文件夹
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="renderer" content="webkit"/>
    <meta name="force-rendering" content="webkit"/>
    <meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1"/>
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title>xxx</title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but xxx doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
  </body>
</html>
```

## src 文件夹
- 新建router.js
  ```js
  import Vue from 'vue'
  import Router from 'vue-router'
  import Main from '@/pages/main/main.vue'
  Vue.use(Router)
  export default new Router({
    routes: [
      { // 当你访问'/'路径，它访问component Helloworld
        path: '/',
        name: 'Main',
        component: Main,
      }, 
    ]
  })
  ```
- 新建store.js
  ```js
  import Vue from 'vue'
  import Vuex from 'vuex'
  Vue.use(Vuex)
  const state = {
    student: {
      age: 21,
    }
  }
  const mutations = {
  } 
  const store = new Vuex.Store({
    state: state,
    mutations: mutations
  })
  export default store
  ```
- 新建api.js
  ```js
  import axios from 'axios'
  import qs from 'qs'
  // axios 配置
  axios.defaults.timeout = 100000; // 超时
  axios.defaults.headers.post['Content-Type'] = 'application/json;charset=UTF-8' // 默认请求头
  axios.defaults.baseURL = 'http://localhost:8080/csy' // 后端API Router注意前缀是 /csy
  // 请求之前可以做什么
  axios.interceptors.request.use((config) => {
    //post请求序列化
    if(config.method === 'post') {
      config.data = qs.stringify(config.data)
    }
    return config //添加这一行
  }, (error) => {
    return Promise.reject(error)
  })
  
  // 返回拦截，可以做些什么
  axios.interceptors.response.use((res) => {
      return res
    }, (error) => {
      return Promise.reject(error)
    }
  )
  
  export default {
    shp2json: (data) => axios.create({headers:{"Content-type":"application/x-www-form-urlencoded"}}).post('/file/shp2json', data), 
  }
  ```
- 新建bus.js
  ```js
  import Vue from "vue"
  export default new Vue()
  ```
- assets文件夹放css，js等资源文件
  * 添加`reset.css`
  * 添加`border.css`
- 将components文件夹重命名为pages
- App.vue修改
  ```js
  <template>
    <div id="app">
      <router-view/>
    </div>
  </template>
  
  <script>
  export default {
    name: 'app',
    components: {
    }
  }
  </script>
  
  <style>
  #app {
    height: 100%;
  }
  </style>
  ```
- main.js修改
  ```js
  import Vue from 'vue'
  import App from './App.vue'
  import router from '@/router/router.js'
  import "@/assets/styles/reset.css"
  import "@/assets/styles/border.css"
  import "@/assets/styles/iconfont.css"
  Vue.config.productionTip = false
  new Vue({
    router,
    render: h => h(App),
  }).$mount('#app')
  ```
- 创建vue.config.js

## 参考
- [2019 年如何搭建你的 Vue 项目](https://zhuanlan.zhihu.com/p/70752505?utm_source=wechat_session&utm_medium=social&utm_oi=726559320958853120)