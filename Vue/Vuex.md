# Vuex
语法|含义
-|-
state        |定义属性（状态 、数据）
getters      |用来获取属性
actions      |定义方法（动作）
commit       |提交变化，修改数据的唯一方式就是显示的提交 mutations
mutations    |定义变化，处理状态（数据）的改变
mapGetters   |用来获取属性（数据）
mapActions   |用来获取方法（动作）

## states 与 getters
### states
在store下新建states文件夹用于模块划分  
```js
export default {
  student: {
    name: "", // 所有的字段都必须提前声明好,不能后续在其他地方新增属性
  }
}
```
在store下新建mutations文件夹,里面分模块划分  
```js
export default {
  update (state, num) {

  }
}
```
### getters
在store下新建getters文件夹,里面有getter.js  
对应的是state, 写在`computed`
```js
export default { // 用于前后端数据联调(转换后端数据格式)
  getFullName () {
    return `${state.firstName} ${state.lastName}`
  }
}
export default () => { // 引入getters
  new Vuex.Store({
    state: state,
    mutations: mutations,
    getters, // 在组件中直接 this.$store.getters.getFullName()
  })
}
```
### 语法糖
- 在组件中`import {mapState, mapGetters} from "vuex"`  
- 使用方法
  * 在组件内
    ```js
    import {mapGetters} from 'vuex'
    computed:mapGetters([
      //此处的 count 与以下 store.js 文件中 getters 内的 count 相对应
      'count'
    ])
    ```
  * 在store.js
    ```js
    // 定义 getters
    var getters={
      //需要传个形参，用来获取 state 属性
      count(state){
        return state.count
      }
    }
    // 创建 store 对象
    const store = new Vuex.Store({
      state,
      getters
    })
    export default store;
    ```

- 若不支持对象展开符
  `npm i -D babel-preset-stage-1`方便使用对象展开运算符  
  配置`babel.config.js`  
  ```js
  // 在组件内
  computed: {
    ...mapState({
      count: (state) => state.count
    }),
    ...mapGetters({
      ["getFullName"]
    })
  }
  ``` 

## mutation
- 官方推荐所有修改state的操作都应该经过`mutation`(虽然也可以直接修改)
  ```js
  export default () => { 
    new Vuex.Store({
      strict: process.env.NODE_ENV === "development" ? true : false, // 开发环境下禁止直接操作 state
      state: state,
      mutations: mutations
    })
  }
  ```
- mutation的参数最多2个, 且第一个是 state
- 语法糖(不必this.$store.commit 或者 this.$store.dispatch 这种语法)
  ```js
  // 在组件内
  export default {
    methods: {
      ...mapActions(["updateAsync"]),
      ...mapMutations(["update"])
    },
    mounted () {
      this.updateAsync(5) 
    }
  }
  ```
  在store.js里
  ```js
  // 定义 actions ，要执行的动作，如流程的判断、异步请求
  const actions ={
    // ({commit,state}) 这种写法是 es6 中的对象解构
    increment({commit,state}){
      //提交一个名为 increment 的变化，名字可自定义，可以认为是类型名，与下方 mutations 中的 increment 对应
      //commit 提交变化，修改数据的唯一方式就是显式的提交 mutations
      commit('increment') 
    }
  }
  
  // 定义 mutations ，处理状态（数据） 的改变
  const mutations ={
    //与上方 commit 中的 ‘increment’ 相对应
    increment(state){
      state.count ++;
    }
  }
  ```

## Actions
Actions允许异步操作state, mutation必须都是同步操作  
一般 actions 用于流程控制,用法同上;但更多还是用于异步操作  
对应的触发方式为： `this.$store.dispatch("updataAsync", 2)`
```js
const actions = {
  decrement({ commit, state }) {
    // **通过动作改变的数据，在此处来做判断是否提交**
    if (state.count > 5) {
      commit('decrement')
    }
  },
  incrementAsync({commit,state}){
    // 模拟异步操作
    var a = new Promise((resolve,reject) => {
      setTimeout(() => {
        resolve();
      }, 3000);
    })
    // 3 秒之后提交一次 increment ，也就是执行一次 increment 
    a.then(() => {
      commit('increment')
    }).catch(() => {
      console.log('异步操作失败');
    })
  }
}
```

## 分模块
文件目录结构
```
├── index.html
├── main.js
├── api
│   └── ... # 抽取出API请求
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # 我们组装模块并导出 store 的地方
    ├── actions.js        # 根级别的 action
    ├── getters.js
    ├── mutations.js      # 根级别的 mutation
    ├── types.js          # 定义常量的地方
    └── modules
        ├── user.js       # 用户模块(其内定义该 user 组件内用到的 state 、 getters 、 actions 和 mutations，并最后统一导出)
        └── products.js   # 产品模块
```
types.js - 定义全局常量
```js
// 定义类型常量，默认全部大写
const INCREMENT = 'INCREMENT'
const DECREMENT = 'DECREMENT'
export default {
  INCREMENT,
  DECREMENT
}
```
user.js 内写该 user 组件内用到的 state 、 getters 、 actions 和 mutations
```js
// 导入 types.js 文件
import types from "./../types";

const state ={
  count:5
}

// 定义 getters
var getters ={
  count(state){
    return state.count
  }
}

const actions ={
  increment({ commit, state }){
    // 此处提交的事件与下方 mutations 中的 types.INCREMENT 对应
    //与原来 commit('increment') 的原理相同，只是把类型名换成了常量
    commit(types.INCREMENT)
  },
  decrement({commit,state}){
    if (state.count>10) {
      // 此处提交的事件与下方 mutations 中的 types.DECREMENT 对应
      commit(types.DECREMENT)
    }
  }
}

const mutations ={
  // 此处的事件为上方 actions 中的 commit(types.INCREMENT)
  [types.INCREMENT](state){
    state.count++
  },
  // 此处的事件为上方 actions 中的 commit(types.DECREMENT)
  [types.DECREMENT](state){
    state.count--
  }
}
// 最后统一导出
export default {
  state,
  getters,
  actions,
  mutations
}
```
getters.js 内写原来的判断奇偶数方法
```js
// 因为数据从 user.js 中获取，所以需要引入该文件
import user from './modules/user'

const getters = {
  isEvenOrOdd(state){
    // 注意数据是从 user.js 中获取的，所以写成 user.state.count
    return user.state.count % 2 == 0 ? "偶数" : "奇数"
  }
}
// 并导出
export default getters
```
actions.js 内写原来的异步操作
```js
// 异步操作中需要用到 increment 方法，所以需要导入 types.js 文件
import types from './types'

const actions= {
    incrementAsync({ commit, state }) {
        // 异步操作
        var p = new Promise((resolve, reject) => {
            setTimeout(() => {
                resolve()
            }, 3000);
        });
        p.then(() => {
            commit(types.INCREMENT);
        }).catch(() => {
            console.log('异步操作');
        })
    }
}
// 最后导出
export default actions;
```
在 index.js 中组装 actions.js 、 getters.js 、user.js 的，然后统一导出
```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

import getters from './getters'
import actions from './actions'
import users from './modules/user'
// 导出 store 对象
export default new Vuex.Store({
    getters,
    actions,
    modules:{
        users
    }
})
```

## 分模块2
```js
import address from './modules/address'

export default () => { 
  new Vuex.Store({
    strict: process.env.NODE_ENV === "development" ? true : false, // 开发环境下禁止直接操作 state
    state: defaultState,
    getters,
    mutations,
    actions,
    modules: {
      address,
    }
  })
}
```
```js
// 在address.js
export default {
  state: {
    addressInfo: {},
    orderAddressId: null // 下单时选择的地址ID
  },
  namespaced：true, // mutations 方法区分模块
  mutations: {
    SET_ADDRESS_INFO: (state, { type, info }) => {
      state.addressInfo[type] = info
    },
  },
  getters: {
    name (state, getters, rootState) { // 支持3个参数(rootState 表示全局)
      return state.name + rootState.name
    }
  }，
  actions: {
    add ({state, commit, rootState}) {
      commit("updateAsync", {num: 5})
    }
  }
}
```
```js
// 在组件中调用时的区分
// state
this.$store.state.address["......"]
// mutation
mutation 默认是不管在哪一模块都添加至全局变量中,因此默认情况下,mutation访问照常,但可以添加属性 namespaced 修改
methods: {
  ...mapMutations(["SET_ADDRESS_INFO"])
  ...mapMutations(["address/SET_ADDRESS_INFO"]) // 区分处于不同模块的mutations的方式
}
// getters

```

## 模板 (单文件模式)
在 store.js
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
  update (state, num) {
    state.student.age = num
  }
}
const store = new Vuex.Store({
  state: state,
  mutations: mutations
})

export default store
export default () => { // 服务端渲染的方式
  new Vuex.Store({
    state: state,
    mutations: mutations
  })
}
```
在 main.js
```js
Vue.use(Vuew) // 与 Vue-router类似
new Vue({
  store, // 注意全局引入
  render: (h) => h(App)
}).$mount("#root")
```
在组件内
```js
this.$store.state["......"]
this.$store.commit("updata", i)
```

## 参考
- [官方文档](https://vuex.vuejs.org/zh/guide/state.html)
- [Vuex 入门](https://www.jianshu.com/p/b014ff74bdb6)
- [Vuex 进阶——模块化组织 Vuex](https://www.jianshu.com/p/a6a8ffea227d)