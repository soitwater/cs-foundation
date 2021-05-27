# Vue经验


## vue 中 vm.$nextTick(cb)实现原理和场景
- 场景：在`dom`更新循环结束后调用，用于获取更新后的`dom`数据
- 原理：`vm.$nextTick(cb)` 是一个异步的方法为了兼容性做了很多降级处理依次有:
  * promise.then
  * MutationObserver
  * setImmediate
  * setTimeout
  * 在数据修改后不会马上更新视图，而是经过`set`方法`notify`通知`Watcher`更新，将需要更新的 `Watcher` 放入到一个异步队列中，`nexTick` 的回调函数就放在 `Watcher` 的后面，等待主线程中同步代码执行借宿然后依次清空队列中，所以 `vm.nextTick(callback)` 是在 `dom` 更新结束后执行的。

## Vue组件中的data为什么必须是一个函数
- 防止重复使用某一子组件时, 若`data`是函数，则引用传递，修改了其中一个`data`, 那么所有的`data`都会被修改
- 例子:
  ```js
  a = Array(3).fill([1]); // [ [1],[1],[1] ]
  a[0][0] = 2; // [ [2],[2],[2] ]
  ```

## `e.target`
```js
Vue获取当前点击元素的兄弟元素，父元素，子元素							
获取dom元素 https://www.cnblogs.com/meng1314-shuai/p/7455575.html							
以及 https://blog.csdn.net/qq_15256443/article/details/82584931							
在<template/>中，<button @click = “getEvent($event)”>点击</button>		

// e.target 是你当前点击的元素							
// e.currentTarget 是你绑定事件的元素							
# 获得点击元素的前一个元素							
e.currentTarget.previousElementSibling.innerHTML							
# 获得点击元素的第一个子元素							
e.currentTarget.firstElementChild							
# 获得点击元素的下一个元素							
e.currentTarget.nextElementSibling							
# 获得点击元素中id为string的元素							
e.currentTarget.getElementById("string")							
# 获得点击元素的string属性							
e.currentTarget.getAttributeNode('string')							
# 获得点击元素的父级元素							
e.currentTarget.parentElement							
# 获得点击元素的前一个元素的第一个子元素的HTML值							
e.currentTarget.previousElementSibling.firstElementChild.innerHTML			
```


## active-class 是哪个组件的属性？
* `<router-link>`
* `exact-active-class`或者`active-class`默认值是`router-link-active`
* router-link 默认情况下的路由是模糊匹配，例如当前路径是 /article/1 那么也会激活 `<router-link to="/article">`
* 当设置 exact-active-class 以后，这个 router-link 只有在当前路由被全包含匹配时才会被激活 exact-active-class 中的class。
* 例如
  - `<router-link to="/article" active-class="router-active"></router-link>`  
    当用户访问 /article/1 时会被激活为`<a href="#/article" class="router-active"></a>`
  - 而当使用`<router-link to="/article" exact-active-class="router-active"></router-link>`  
    当用户访问 /article/1 时，不会激活这个link的class`<a href="#/article"></a>` 

## `keep-alive`原理
* 使用
  ```js
  // 在 router.js
   meta:{keepAlive: false}
  // 使用 v-if 或者 路由守卫判断是否需要 缓存
   <keep-alive>
    <router-view v-if="$route.meta.keepAlive"></router-view>
  </keep-alive>
  <router-view v-if="!$route.meta.keepAlive"></router-view> 
  ```
* 其实就是在组件created时将需要缓存的VNode节点保存在this.cache中  
  - 在render时,如果VNode的name符合在缓存条件（可以用include以及exclude控制），
  - 则会从this.cache中取出之前缓存的VNode实例进行渲染。

## `.sync`运算符  
* 用于`prop`中,当子组件的数据修改时,将导致父组件的数据也修改(违背了单项数据流的初衷,故后来`.sync`成了一个语法糖),其实际作用如下：
* `<child  :foo.sync='msg'></child>`  就会被扩展为：`<child  :foo='bar'  @update:foo='val => bar = val'>`     
  - 当子组件需要更新 foo 的值的时候，他需要显示的触发一个更新事件: `this.$emit('update:foo', newValue)`  

## 接收$emit的传值时的简写
- `<navigator @getSaleInfoList2selltable="listData = $event" />`这里的$event相当于下面的`param`:
  ```js
  this.$emit("getSaleInfoList2selltable", param);
  ```

## 操纵Vue实例的原型链：为所有的Vue实例添加共有变量  
- 例如：
  ```js
  // 在main.js
  import Vue from 'vue'
  import axios from 'axios'
  Vue.prototype.$axios = axios
  
  // 在其他Vue单文件,不必引入 import axios from 'axios' ,即可使用 axios
  this.$axios.get()
  ```

## Vue对不同构建版本的解释
### 分类
| -                         | UMD                | CommonJS              | ES Module (基于构建工具使用) | ES Module (直接用于浏览器) |
| ------------------------- | ------------------ | --------------------- | ---------------------------- | -------------------------- |
| 完整版                    | vue.js             | vue.common.js         | vue.esm.js                   | vue.esm.browser.js         |
| 只包含运行时版            | vue.runtime.js     | vue.runtime.common.js | vue.runtime.esm.js           | -                          |
| 完整版 (生产环境)         | vue.min.js         | -                     | -                            | vue.esm.browser.min.js     |
| 只包含运行时版 (生产环境) | vue.runtime.min.js | -                     | -                            | -                          |
### 术语
- 在`NPM`包的`dist/`目录你将会找到很多不同的`Vue.js`构建版本。
- `完整版`：同时包含`编译器`和`运行时`的版本。
- `编译器`：用来将模板字符串编译成为 JavaScript 渲染函数的代码。
- `运行时`：用来创建 Vue 实例、渲染并处理虚拟 DOM 等的代码。基本上就是除去编译器的其它一切。
- `UMD`：UMD 版本可以通过 `<script>` 标签直接用在浏览器中。`jsDelivr CDN` 的 `https://cdn.jsdelivr.net/npm/vue` 默认文件就是`运行时 + 编译器`的 UMD 版本 (vue.js)。
- `CommonJS`：CommonJS 版本用来配合老的打包工具比如 Browserify 或 webpack 1。这些打包工具的默认文件 (pkg.main) 是只包含运行时的 CommonJS 版本 (vue.runtime.common.js)。
- `ES Module`：从 2.6 开始 Vue 会提供两个 ES Modules (ESM) 构建文件：
  * `为打包工具提供的 ESM`：为诸如 webpack 2 或 Rollup 提供的现代打包工具。ESM 格式被设计为可以被静态分析，所以打包工具可以利用这一点来进行`tree-shaking`并将用不到的代码排除出最终的包。为这些打包工具提供的默认文件 (pkg.module) 是只有运行时的 ES Module 构建 (vue.runtime.esm.js)。
  * `为浏览器提供的 ESM (2.6+)`：用于在现代浏览器中通过`<script type="module">`直接导入。
### 运行时 + 编译器 vs. 只包含运行时
- 如果你需要在客户端编译模板 (比如传入一个字符串给 `template` 选项，或挂载到一个元素上并以其 DOM 内部的 HTML 作为模板)，就将需要加上编译器，即完整版：
  ```js
  // 需要编译器
  new Vue({
    template: '<div>{{ hi }}</div>'
  })

  // 不需要编译器
  new Vue({
    render (h) {
      return h('div', this.hi)
    }
  })
  ```
- 当使用 `vue-loader` 或 `vueify` 的时候，`*.vue` 文件内部的模板会在构建时预编译成 JavaScript。你在最终打好的包里实际上是不需要编译器的，所以`只用运行时版本`即可。
- 因为运行时版本相比完整版体积要小大约 `30%`，所以应该尽可能使用这个版本。如果你仍然希望使用完整版，则需要在打包工具里配置一个别名：
  ```js
  webpack
  module.exports = {
    // ...
    resolve: {
      alias: {
        'vue$': 'vue/dist/vue.esm.js' // 用 webpack 1 时需用 'vue/dist/vue.common.js'
      }
    }
  }
  ```

## 给v-html元素添加样式
- `scoped`属性导致css仅对当前组件生效（用css3的属性选择器+生成的随机属性实现的）
- 而`v-html`绑定渲染出的内容可以理解为是子组件的内容，子组件不会被加上对应的属性，所以不会应用css.解决的话把scoped属性去掉就行了
- 或者在选泽器前添加`>>>`
- 例子
  ```css
  <style scoped>
    >>> p {
      font-size: 14px;
    }
  </style>

  /* 会被编译为类似下面的格式 */
  [data-v-146ebe36] p {
    font-size: 14px;
  }
  ```

## Vue缓存步骤
### 在App.vue
```vue
<template>
  <div id="app">
    <keep-alive>
      <router-view v-if="$route.meta.keepAlive" />
    </keep-alive>
    <router-view v-if="!$route.meta.keepAlive" />
  </div>
</template>
```
### 在`router.js`路由中
```js
{
  path: '/mine',
  name: 'Mine',
  // 增加`keepAlive`属性
  meta: { title: 'Mine', keepAlive: true },
  component: () => import("@/pages/Mine/Mine.vue"),
}
```
### 有了前2步，就已经实现了缓存。但有时希望从A页面到B页面时，不要缓存，那么就可以...
```js
// 在需要做缓存的组件内
export default {
  beforeRouteEnter(to, from, next) {
    to.meta["fromPath"] = from.path;
    // 从 /help 跳转到当前组件时，则设置当前组件的`meta`的`needFresh`为`true`
    if (from.path === "/help") {
      to.meta["needFresh"] = true;
    } else {
      to.meta["needFresh"] = false;
    }
    next();
  },
  activated() {
    if (this.$route.meta["needFresh"]) {
      // 表示初始化数据
      this.init();
    }
  },
}
```

## 如何正确引用图片地址
### 例题
- 像这样的图片，放在模板`<template>`(html),在webpack打包后可以被访问到
  ```html
  <img src="./../assets/demo.png" />
  ```
- 但假如把地址提取出来后放进Vue实例的data中，那么在webpack打包后就访问不到
  ```js
  <img :src="url" >
  export default {
    data () {
      return {
        url: './../assets/demo.png'
      }
    }
  }
  ```
### 原因：
- 放在`<template>`模版里是会被 `webpack` 打包出来 所以可以
  * 可以使用`require`,( `Webpack`在解析`template`时,遇到`img`标签时,如果`src`是静态字符串且不是以`http`等开头,那么`webpack`会认为是相对源码目录的图片 )
    ```html
    <img
      :src="require('../../../assets/logo.png')"
      :alt="logo"
    />
    ```
- 而在 js 里写图片路径其实只是字符串, webpack 不会处理
- 如果想这么做 你可以把图片放在最外层的 static 文件夹
- 或使用 import require 引入 `import url from './../assets/demo.png'`


## Vue.set()
### 应用场景
- 向Vue实例的data对象修改其属性或插入新的属性  
- 理由:  
  > 由于Javascript的限制，Vue不能自动检测以下变动的数组。  
  > 当你利用索引直接设置一个项时，vue不会为我们自动更新。  
  > 当你修改数组的长度时，vue不会为我们自动更新。  
  `son.vue`文件
  ```html
  <template>
    <div>
      <div>a&nbsp;{{arr[0]}}</div>
      <div>b&nbsp;{{arr[1]}}</div>
      <div @click="doSomething">click&nbsp;me</div>
    </div>
  </template>
  
  <script>
  import store from "@/store/store.js"
  export default {
    name: 'Son',
    data () {
      return {
        arr: ["zero", "one"]
      }
    },
    methods: {
      doSomething () {
        // this.arr[1] = "changed" // 不会更新视图
        // this.$set(this.arr, 1, "changed") // 会更新视图
        this.arr.splice(1, 1, "changed") // 会更新视图
      }
    }
  }
  </script>
  ```
- 除了向Vue实例的data对象插入/修改属性，还可以向Vue实例插入属性
  * Vue实例中只有data对象是响应式的
  * 使用Vue.set()可以添加属性到Vue实例上（误?）


## Vue优点
- 双向数据绑定：视图 / 数据的分离
- 组件化
- 虚拟DOM：比操作原生dom更快


## vue监听路由变化
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

## 为什么vue组件中data必须是一个函数？
- 防止组件复用时，在A处修改了`data`后，B处的`data`也被意外修改
- 把`data`写成函数，使用返回对象的函数时，由于每次返回的都是一个新对象（Object的实例），引用地址不同，则不会出现这个问题

## v-for key的作用
- key属性的类型只能为 `string`或者`number`类型，用于标识每个节点
- 它的作用体现在`VDom`的`diff`算法中
- key 的作用类似于数据库的`主键`概念，`唯一标识` + `复用`
- 例子
  ```html
  <div>1</div>
  <div>2</div>
  <div>3</div>
  
  <!--
  这三个<div>，可以看作 [1,2,3]
  现在需要删除<div>2</div>, 那么计算机就是遍历，
  
   -->
  ```

## vue模拟hover事件
- 注意`.native`
  * 给普通的标签加事件，然后加`native`是无效的
  * 监听组件根元素的原生事件，主要是给自定义的组件添加原生事件
- vue中通过绑定`mouseover`和`mouseout`事件来模拟`hover`事件
  ```js
  <el-button type="primary" icon="el-icon-search" plain round 
    @mouseenter.native="knowledge = '即将推出，敬请期待'" 
    @mouseleave.native="knowledge = '知识库搜索答案'">
    {{knowledge}}
  </el-button>
  ```

## 按需加载组件
- 在`router.js`中这么写
  ```js
  {
    path: "/father",
    name: "Father",
    component: () => import('@/pages/test/index.vue'),
  },
  ```
- 怎么证明组件是异步加载的(一定要记得验证)  
  有的时候,html模板是会自动引入全部的js文件,导致异步加载无效(看页面的html文件是否引入应异步加载的js文件)
  在.vue文件中  
  ```js
  <script>
  console.log("async component") // 假如是异步的,它会在路由跳转后才打印这句话
  export default {
    data() {
      return {}
    }
  }
  </script>
  ```

## 全局变量
- 在`main.js`中
  ```js
  Vue.prototype._global = {
    _a: 1
  }
  ```
- 在需要的组件内`this._global`即可

## 为什么Vue中对mouseover的监听无效
```html
<!-- 加上 .native -->
<el-menu-item index="1" @mouseover.native="showUp">find Music</el-menu-item>
```

## Vue全局组件的实现

### 方法一：外部UI库的开发
- 略

### 方法二：Vue.use()
一般的UI库都会使用这种方法来进行按需引入    
- 在src/components/message/message.vue
```js
<template>
  <div class="message" :class="type" v-show="show">
    <span class="text">{{text}}</span>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    name: 'message',
    props: {
      type: {
        type: String,
        default: 'info',
        validator: val => ['success', 'error'].includes(val)
      },
      text: {
        type: String,
        default: ''
      },
      show: {
        type: Boolean,
        default: false
      }
    }
  }
</script>

<style scoped>
  .message {
    position: fixed;
    top: 40px;
    text-align: center;
    left: 50%;
    transform: translateX(-50%);
    min-width: 400px;
    background: #f5f5f5;
    border-radius: 4px;
    z-index: 1000;
  }
  .message.error {
    color: red;
  }
  .message.success {
    color: greenyellow;
  }
</style>
```
- 在`src/components/message/index.js`
```js
import Message from './Message.vue'

const MESSAGE = {
  duration: 3000, // 显示的时间 ms
  animateTime: 300, // 动画时间,表示这个组件切换show的动画时间
  install(Vue) {
    if (typeof window !== 'undefined' && window.Vue) {
      Vue = window.Vue
    }
    Vue.component('Message', Message) // 全局注册组件

    function msg(type, text, callBack) {
      let msg
      let duration = MESSAGE.duration
      if (typeof text === 'string') {
        msg = text
      } else if (text instanceof Object) {
        msg = text.text || ''
        if (text.duration) {
          duration = text.duration
        }
      }
      let VueMessage = Vue.extend({
        render(h) { // 不用 template 选项是为了避免多引入一个 vue-template 编译代码,避免增大依赖库体积
          let props = {
            type,
            text: msg,
            show: this.show
          }
          return h('message', { props })
        },
        data() {
          return {
            show: false
          }
        }
      })
      let newMessage = new VueMessage()
      let vm = newMessage.$mount()
      let el = vm.$el
      document.body.appendChild(el) // 把生成的提示的dom插入body中
      vm.show = true
      let t1 = setTimeout(() => {
        clearTimeout(t1)
        vm.show = false  //隐藏提示组件，此时会有300ms的动画效果，等动画效果过了再从body中移除dom
        let t2 = setTimeout(() => {
          clearTimeout(t2)
          document.body.removeChild(el) //从body中移除dom
          newMessage.$destroy()
          vm = null // 设置为null，好让js垃圾回收算法回收，释放内存

          callBack && (typeof callBack === 'function') && callBack()
        }, MESSAGE.animateTime)
      }, duration)
    }

    Vue.prototype.$message = { // 将组件 message 挂载到vue原型上，暴露其中的方法
      success(text, callBack) {
        if (!text) return
        msg('success', text, callBack)
      },
      error(text, callBack) {
        if (!text) return
        msg('error', text, callBack)
      },
    }
  }
}
export default MESSAGE
```
- 在main.js
```js
import vMessage from '@/components/message/index.js'
Vue.use(vMessage)
```
- 使用,在其他的组件内部直接使用
```js
this.$message.success('普通消息')
```

### 方法三：Vue.component()
- Vue.component() 与 Vue.use()的区别在于:
  * 后者需要提供一个install方法,在install方法里其实调用的还是Vue.component()  


## 在 Vue 中，子组件为何不可以修改父组件传递的 Prop ?
- 因为vue设计是单向数据流，数据的流动方向只能是自上往下的方向；
- 易于监测数据的流动，出现了错误可以更加迅速的定位到错误发生的位置。
- 当子组件可以直接修改父组件的 prop 时，会导致其他用到此props的子组件的值也发生改变，导致数据混乱，以及难以追踪的bug。如果props是基础类型，不会造成其他影响，但也会是整个组件数据混乱。


## 如果修改了，Vue 是如何监控到属性的修改并给出警告的。
- 在initProps的时候，在defineReactive时通过判断是否在开发环境，如果是开发环境，会在触发set的时候判断是否此key是否处于updatingChildren中被修改，如果不是，说明此修改来自子组件，触发warning提示。
- 需要特别注意的是，当你从子组件修改的prop属于基础类型时会触发提示。 这种情况下，你是无法修改父组件的数据源的， 因为基础类型赋值时是值拷贝。你直接将另一个非基础类型（Object, array）赋值到此key时也会触发提示(但实际上不会影响父组件的数据源)， 当你修改object的属性时不会触发提示，并且会修改父组件数据源的数据。

