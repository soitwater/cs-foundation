# Vue-Cli创建一个项目
Vue-Cli创建一个项目后需要做的事情

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
    shp2json: (data) => axios.create({headers:{"Content-type":"application/x-www-form-urlencoded"}}).post('/file/  shp2json', data), 
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