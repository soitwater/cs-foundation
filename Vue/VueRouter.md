# VueRouter

## 概念
前端路由是相对于后端路由而言：
- 前端路由是SPA的核心，改变页面展示内容且无网络延迟，本质是DOM的隐藏/显示
- 后端路由需向服务器发送请求新页面

路由分为2种：基于HTMLl5 history api; 基于Hash
- 前者以"/"分割，改变URL页面其实没跳转，需要服务端支持，即不同的URL都指向同一个HTML文件
- 后者以"#"分割

## 示例
1. 安装 npm i vue-router -S
2. 创建router.js, 内容如下:
   ```js
   import Vue from 'vue'
   import Router from 'vue-router'
   
   import Login from '@/pages/login/Login'
   Vue.use(Router)
   export default new Router({
     routes: [{ // 当你访问'/'路径，它访问component Login
         path: '/',
         name: 'Login',
         component: Login,
       }, { // 移动页面入口
         path: '/apply',
         name: 'Apply',
         component: Apply,
         meta: {
           requireAuth: true, // 自定义字段，表示进入这个字段是需要登陆的
         },
       }
     ]
   })
   ```
3. 在main.js中, 需要：
   ```js
   import router from './router'
   // 导入 router
   new Vue({
     el: '#app',
     router,
     render: h => h(App),
     components: { App },
     template: '<App/>'
   })
   // 路由守卫
   router.beforeEach((to, from, next) => { // 避免未登陆直接访问其他页面
     if (to.matched.some(record => record.meta.requireAuth)) { // 判断该路由是否需要登录权限
       if (store.state.isLogin) { // 判断当前的token是否存在 ； 登录存入的token
         next()
       } else {
         next({
           path: '/',
           query: {redirect: to.fullPath} // 将跳转的路由path作为参数，登录成功后跳转到该路由
         })
       }
     } else {
       next()
     }
   })
   ```
4. 改变router  
   ```js
   this.$router.push({
     path: "apply",
   })
   ```
   或者
   ```html
   <router-link to="/">click me</router-link>
   ```

## API  
1. 分辨route, routes 与 router
   ```js
   // 如下是一个路由 route
   {
     path: '/',
     name: 'Login',
     component: Login,
   }
   // 一组route即为routes
   routes: [{ 
       path: '/',
       name: 'Login',
       component: Login,
     }, { 
       path: '/apply',
       name: 'Apply',
       component: Apply,
       meta: {
         requireAuth: true, 
       },
     }
   ]
   // router 路由管理器
   new Router({
     routes: []
   })
   ```
2. 获取路由参数
   ```js
   this.$route.params  // 类似于 /detail/:id  即 /detail/1
   this.$route.query // 类似于  /detail?id=1
   ```

## 参考
- [vue-router 基本使用](https://www.cnblogs.com/SamWeb/p/6610733.html)
- [官方文档](https://router.vuejs.org/zh/guide/#html)