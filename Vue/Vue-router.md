# Vue-router

## 创建
- 新建routes.js 以及 router.js
- router.js
  ```js
  export default () => {  // 包括一些配置
    return new Router({
      routes,
    })
  }
  ```
- routes.js
  ```js
  const routes = : [
    {
      path: '/',        // 路径
      name: 'Main',
      component: Main,
    }
  ]
  ```

## router 配置
- 在`App.vue`中配置`<router-view/>`
- 重定向
  ```js
  { // 当路由是 "/" 时会跳转到新路由 "/app"
    path: "/",
    redirect: "/app"
  }
  ```
- 更改`hash`路由
  hash路由多用于定位(页面锚点)(旧),history才是用于记录状态的(新)  
  因为hash本意是做页面定位(锚点),如果将hash用于路由,那么原来的锚点将无法使用(因此后来又设计了history)  
  且hash不会被搜索引擎解析  
  修改路由模式：  
  ```js
  export default () => {
    return new Router({
      routes,
      mode: "history"     // hash
    })
  }
  ```
- `base`路由前缀(不常用)  
  将在浏览器的路由前添加前缀 "/base/"(即:基路径)  
  不过在路由访问时, 即使不添加前缀也依然可以访问  
  ```js
  export default () => {
    return new Router({
      routes,
      base: "/base/"    
    })
  }
  ```
- `linkActiveClass`与`linkExactActiveClass`
  * 与`<router-link :to=""></router-link>`搭配, 当页面路由跳转至`to`时,`<router-link>`会被自动添加类名
  * 二者的区别:
    - 若有两个路由,前者为`/login`,后者为`/login/exact`,则：
    - `<router-link to="/login"></router-link>`的`classList`为`"active-link exact-active-link"`
    - `<router-link to="/login/exact"></router-link>`的`classList`仅为`"exact-active-link"`
    - 后者比前者精确
    - 使用场景有限
  ```js
  export default () => {
    return new Router({
      routes,
      linkActiveClass: "active-link",                  // 值其实是一个类名
      linkExactActiveClass: "exact-active-link"        // 值依旧是一个类名
    })
  }  
  ```
- 为什么使用`<router-link>`而不是`<a>`
  * 因为`<a>`的`href`属性是写死的
  * `<a>`的默认行为是跳转
  * `<router-link>`其实是添加了事件的`<a>`标签

- scrollBehavior
  ```js
  export default () => {
    return new Router({
      routes,
      scrollBehavior (to, from, savedPosition) { // 定义切换页面时如何滚动
        if (savedPosition) { // 若缓存过滚动条,则滚动到原来滚动条的位置
          return savedPosition
        }
        if (!to.meta.keepAlive) { // 滚动到页面起始位置
          return {
            x: 0,
            y: 0
          }
        }
      }
    })
  }  
  ```
- parseQuery 与 stringifyQuery(少用)   
  指定url后所携带参数的转换方法
  ```js
  export default () => {
    return new Router({
      routes,
      parseQuery (query) {}
      stringifyQuery (obj) {}
    })
  }  
  ```
- fallback  
  * 当浏览器不支持 history.pushState 控制路由是否应该回退到 hash 模式。默认值为 true。
  * 在 IE9 中，设置为 false 会使得每个 router-link 导航都触发整页刷新。它可用于工作在 IE9 下的服务端渲染应用，因为一个 hash 模式的 URL 并不支持服务端渲染
  ```js
  export default () => {
    return new Router({
      routes,
      fallback
    })
  }  
  ```

## routes 路由
- `this.$route` 获取路由全部信息(具体自己打印)
  * query(vue-router自动解析query)
  * parans
- 路由分类
  * 动态路由匹配
  * 嵌套路由
  * 编程式路由
  * 命名路由和命名视图
### 动态路由匹配
| 模式                         | 匹配路径            | $route.params                  |
| ---------------------------- | ------------------- | ------------------------------ |
| /user/:username              | /user/evan          | {username: 'evan'}             |
| /user/:uername/post/:post_id | /user/evan/post/123 | {username:'evan', post_id:123} |
- 使用：
  ```js 
  （1）在router/index.js里，添加一个路由：
      {
        path: '/test2/:ID',
        name: 'Test2',
        component: Test2
      }
  （2）再创建一个test2.vue，结果如下：
      <div>
          This is your ID
          <span>{{$route.params.ID}}</span>
      </div>
  （3）在浏览器中访问 http://localhost:8081/#/test2/3
       此时页面显示： This is your ID 3  

  同理，这条动态路由可以很长的：
  router/index.js里：
      {
        path: '/test2/:test2id/name/:name/code/:code',
        name: 'Test2',
        component: Test2
      }
  在test2.vue里：
    <div>
      This is your ID
      <span>{{$route.params.id}}</span><hr/>
      This is your name
      <span>{{$route.params.name}}</span> <hr/>
      This is your code
      <span>{{$route.params.code}}</span>
    </div> 
  ```

### 嵌套路由
路由中嵌套路由，主要是用了children
```js
NO.1 在router/index.js中，添加routes，如下：
    {
      path: '/test2',
      name: 'Test2',
      component: Test2,
      children: [{ // children是数组，其元素是Object
        path: 'picture', // 不要有斜杆，不要有components等其他任何东西，就是一个vue文件名字就行了
        name: 'Picture',
        component: Picture
      }, {
        path: 'title',
        name: 'Title',
        component: Title
      }]
    }
NO.2 在router/index.js中，导入：
import Test2 from '@/pages/test2/test2'
import Picture from '@/pages/test2/components/picture' // 正常地导入即可
import Title from '@/pages/test2/components/title'
NO.3 test2.vue的内容
  <div>
    This is Test2
    <router-link to='/test2/picture'>显示图片</router-link>
    <router-link to='/test2/title'>显示标题</router-link> // 注意 to='' 引号里是router/index.js里的内容，严格按照 '/父路由/子路由' 的形式书写
    <div>
      <router-view></router-view>  // 这里显示picture.vue或者title.vue的内容
    </div>
  </div>  
```

### 编程式路由
```js
通过JS代码来实现页面的跳转,如：
$router.push("name")
$router.push({path: "name"})
$router.push({path:"name?a=123"}) 或者 $router.push({path:'name', query: {a: 123}}) // 可加入参数，两者都是明文传递的
$router.go(1) // $router 其实就是对 history 的封装
如：
NO.1 在router/index.js中先添加 path: '/picture'
NO.2 在test2.vue中，添加一个<button/> 它绑定事件 @click='jump'
NO.3 jump事件
      jump () {
        //   this.$router.push({path:'picture',query: {id: 100, name:'xiao2'}})
        this.$router.push('/picture?id=100&name=xiao') // 使用 & 传递多个值
      }
NO.4 在picture.vue使用
    <span>{{$route.query.id}}</span>
    <span>{{$route.query.name}}</span>
来接受参数，注意其与{{$route.params.id}} 的区别，后者在编程式路由中使用是错误的，它应该出现在 router/index.js中所定义的 path: '' (动态路由)中
NO.5 使用back () {
          this.$router.go(-1)
      }，可以回退到这一页面哦
```
### 命名路由与命名视图
次要，了解。  
给路由定义不同的名字，根据名字进行匹配；  
给不同的router-view定义名字，通过名字进行对应组件的渲染；  
```js
如： <router-link v-bind:to='{path: "/picture"}'>跳转到picture</router-link>
// 注意 to 前面要使用 v--bind  
// to的值 使用name 来定向也是可以的 v-bind:to='{name: "picture"}'

假如命名的是一个 动态路由， 那么 v-bind:to='{name: "picture", params: {id: 123}}'
注意这里的params使用 {{$route.params.id}} 来接收
```
## routes 配置
- name 与 meta 与 children
  ```js
  {
    path: '/main',       
    name: 'Main',  // 可用于<router-link :to="{name: 'Main'}">
    component: Main,
    meta: { // 页面的源信息，利于SEO，用于路由守卫
      title: "this is ...",
      description: "this is ..."
    },
    children: [ // 格式同外层 routes
      {
        path: "test", // 即实际路由 "/main/test"
        component: Test // 注意：需要在父组件 Main 上声明 <router-view> 以有位置显示“嵌套路由”           
      }
    ]
  }
  ```
- 可以在 `<router-view>`外添加 `<transition name="fade"></transition>`，表示过渡动画，其中动画`fade`需要自定义
  ```css
  /* 在样式表添加 */
  .fade-enter-active .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter .fade-leave {
    opacity: 0;
  }
  ```

## 常用API
```js
this.$router.push({
  path: '',
})

this.$router.replace({ // .replace 会影响路浏览器的 后退按钮
  path: '',
  query: {

  }
})

this.$route.query.xxx
```

## 命名视图
多用于三栏布局
```html
在同一组件
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```
```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo, // 看这里
        a: Bar,
        b: Baz
      }
    }
  ]
})
```

## 导航守卫
3种  
- 全局导航钩子
  * router.beforeEach(to, from, next), `// to 即将进入的路由; from 正要离开的路由; `
  * router.beforeResolve(to, from, next),
  * router.afterEach(to, from ,next)
- 组件内钩子
  * beforeRouteEnter,
  * beforeRouteUpdate,
  * beforeRouteLeave
- 单独路由独享组件
  * beforeEnter
### 完整的导航解析流程
- 导航被触发。
- 在失活的组件里调用离开守卫。
- 调用全局的 beforeEach 守卫。
- 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
- 在路由配置里调用 beforeEnter。
- 解析异步路由组件。
- 在被激活的组件里调用 beforeRouteEnter。
- 调用全局的 beforeResolve 守卫 (2.5+)。
- 导航被确认。
- 调用全局的 afterEach 钩子。
- 触发 DOM 更新。
- 用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。
### 全局守卫
在`main.js`,规定每次执行路由前都会调用此钩子函数    
以下三个钩子函数按顺序执行
```js
router.beforeEach((to, from, next) => {
  // 常用于数据的校验
  if (to.fullPath === "/app") { // 验证部分页面需要用户先登录
    next("/login") 
  }
})
```
```js
router.beforeResolve((to, from, next) => {
  // 与前一钩子类似,区别在于执行顺序
  next()
})
```
```js
router.afterEach((to, from) => {
  
})
```
### 路由独享的守卫
```js
routes: [
  {
    path: '/foo',
    component: Foo,
    beforeEnter: (to, from, next) => {
      // ...
      next()
    }
  }
]
```
### 组件内部守卫
组件内  
```js
export default {
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
    next()
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
    next()
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
    next() // 有 next 参数时需执行 next() ,否则路由终端
  }
}
```


## 模板
```js
import Vue from 'vue'
import Router from 'vue-router'
import Main from '@/pages/main/main.vue' // 引入组件
import otherRoutes from './otherRoutes.js' // 引入
Vue.use(Router)

const routes = : [
  {
    path: '/',        // 路径
    name: 'Main',
    component: Main,
  }
].concat(otherRoutes) // 将routes拆分到其他JS文件,方便管理

export default new Router({ // 导出(全局共用一个 router 对象)
  routes
})

// 如果全局共用一个 router， 
// 每次服务端都会生成一个新的 app，而 router 只有一个对象，就会缓存每次新建的 app，
// 导致 app 对象在服务端渲染完成之后没有被释放掉，最后内存溢出。
export default () => { // 导出(用于SSR, 避免内存溢出)
  return new Router({  
    routes
  })
}
// SSR的在main.js中是这么做的
import createRouter from "./随便/router.js"
new Vue({
  createRouter() // 注意这里
  render: h => h(App),
}).$mount('#app')
```
在main.js文件中
```js
import router from '@/router/router.js'
new Vue({
  router, // 注意这里
  render: h => h(App),
}).$mount('#app')
```

## 三级路由 & 二级路由
- 路由`<router-view />`最好是绝对定位
- 三级路由(在`router.js`中三级路由在二级路由的`children`内)应在二级路由组件(vue文件)内存在`<router-view />`
  ```js
  <template>
    <div>
      <span>二级路由内容</span>
      <!--三级路由-->
      <router-view />
    </div>
  </template>
  ```


## 参考
- [官方文档](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#%E7%BB%84%E4%BB%B6%E5%86%85%E7%9A%84%E5%AE%88%E5%8D%AB)