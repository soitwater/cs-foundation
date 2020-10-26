# vue监听路由变化
```js 
let vm = new Vue({
 el: "#app",
 data: {},
 router,
 watch: {
  '$route.path': function (newVal, oldVal) {
   if (newVal === '/login') {
    console.log('欢迎进入登录页面');
   }
   if (newVal === '/register') {
    console.log('欢迎进入注册页面');
   }
  }
 }
})
```