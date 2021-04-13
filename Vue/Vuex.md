# vuex写法

## 官方文档
- [官方文档](https://vuex.vuejs.org/zh/guide/modules.html)

## 几个概念
- state: 数据仓库
- getters: 
  * 相当于`computed`
- mutations
- actions
- --
- commit: 组件内提交到`mutations`(同步操作)
- dispatch：
- --
- mapGetters
- mapActions
- mapState

## 基础写法
- 在`Home.vue`中
  ```html
  <template>
    <div class="home" @click="handleClick">同步：{{myName}}</div>
    <div class="home" @click="handleClickAsync">异步：{{myName}}</div>
  </template>

  <script>

  export default {
    name: 'Home',
    computed: {
      myName() {
        return this.$store.state.name;
      }
    },
    methods: {
      handleClick() {
        // 同步修改的操作直接 this.$store.commit('名字'), 这里的'名字'是放在`mutations`里的
        this.$store.commit('changeName', '小红');
      },
      handleClickAsync() {
        // 异步修改的操作需要 this.$store.dispatch('名字'), 这里的'名字'是放在`action`里的; `action`里执行了异步`await`, 再去 this.commit('名字'), 这里的'名字'是放在`mutations`里的
        this.$store.dispatch('changeNameAsync', '小红');
      },
    }
  }
  </script>
  ```
- 在`src/store/index.js`
  ```js
  import { createStore } from 'vuex'

  export default createStore({
    state: {
      name: '小明',
    },
    // 只有 mutation 能改 state 中的数据,而且 mutation 是`同步`的代码;否则 vue-devtool 监测不到数据的变化
    mutations: {
      // state 表示本作用域内(上面)的 state
      // data 是传入的数据
      changeName(state, data) {
        state.name = data;
      },
    },
    // action 只是执行`异步`操作的部分,真正操作state中的数据的部分还是放在mutations中
    actions: {
      // store 表示整个 vuex 实例
      // data 是传入的数据
      // 异步的操作的流程是：在组件this.$store.dispatch -> 在这里执行异步 -> 再异步的resolve后: store.commit('mutations的key', data);
      changeNameAsync(store, data) {
        setTimeout(() => {
          store.commit('changeName', data);
        }, 2000);
        
      }
    },
    modules: {

    }
  })
  ```

## Composition Api 写法
- Home.vue
  ```html
  <template>
    <div class="home" @click="handleClick">{{name}}</div>
    <div class="home" @click="handleClickAsync">{{name}}</div>
  </template>

  <script>
  import { useStore } from 'vuex';
  import { toRefs } from 'vue';

  export default {
    name: 'Home',
    setup(props, context) {
      // 1. 获取全局的 store 对象
      const store = useStore();
      const { name } = toRefs(store.state);
      const handleClick = () => {
        store.commit('changeName', '小红');
      };
      const handleClickAsync = () => {
        store.dispatch('changeNameAsync', '小刚');
      };

      return { name, handleClick, handleClickAsync };
    },
  }
  </script>
  ```
- store.js
  ```js
  import { createStore } from 'vuex'

  export default createStore({
    state: {
      name: '小明',
    },
    mutations: {
      changeName(state, data) {
        state.name = data;
      },
    },
    actions: {
      changeNameAsync(store, data) {
        setTimeout(() => {
          store.commit('changeName', data);
        }, 2000);
      },
    },
    modules: {

    }
  })
  ```

## 完整写法
- 分模块
  ```js
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
      ├── mutations.js      # 根级别的 mutation
      └── modules
          ├── cart.js       # 购物车模块
          └── products.js   # 产品模块
  ```
- Home.vue
  ```html
  <template>
    <div class="home">{{name}}</div>
    <div class="home">{{roles}}</div>
    <div class="home">{{whiteList}}</div>
    <button class="home" @click="handleClickRolesAsync">异步设置roles：{{roles}}</button>
    <button class="home" @click="handleClickWhitelistAsync">异步设置whiteList：{{whiteList}}</button>
  </template>

  <script>
  import { useStore, mapState, mapGetters, mapMutations } from 'vuex';
  import { toRefs } from 'vue';

  export default {
    name: 'Home',
    computed: {
      // 对象扩展符写法
      ...mapState({
        name: state => state.name,
        // 相当于： age: state => state.age,
        age: 'age',
        // 也可以获取子模块下的 count
        count: state => state.user.count,
      }),
      ...mapGetters({
        // 把 `this.roles` 映射为 `this.$store.getters.roles`
        roles: 'roles',
      })
    },
    mounted() {
      console.log(`1646 computed计算属性; name: ${this.name}; age: ${this.age}; user.count: ${this.count};`);
      console.log(`1647 computed计算属性; roles: ${this.roles}, 类型: ${typeof this.roles};`);
    },
    methods: {
      ...mapMutations({
        // 将 `this.setName()` 映射为 `this.$store.commit('changeName')`
        // mapState, mapGetters, mapMutations 其实都没什么用, 不过是简写一些'链式写法', 但意义不大
        setName: 'changeName',
      })
    },
    setup(props, context) {
      const store = useStore();
      const { name } = toRefs(store.state);
      const { roles } = toRefs(store.getters);
      const { whiteList } = toRefs(store.state.user);

      const handleClickRolesAsync = async () => {
        let res = await store.dispatch('user/setRoutes');
        // 打印：SET_ROUTES_SUCCESS!
        console.log('1621', res);
      };
      const handleClickWhitelistAsync = () => {
        store.dispatch('user/setWhiteList');
      };

      return { name, roles, whiteList, handleClickRolesAsync, handleClickWhitelistAsync };
    },
  }
  </script>
  ```
- src/store/index.js
  ```js
  import { createStore } from 'vuex'
  import getters from './getters.js';

  // webpack api - 可以创建上下文,自动导入模块,不必每次手动导入
  const modulesFiles = require.context('./modules', true, /\.js$/);
  const modules = modulesFiles.keys().reduce((modules, modulePath) => {
    const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1');
    const value = modulesFiles(modulePath);
    modules[moduleName] = value.default;
    return modules;
  }, {});

  export default createStore({
    // 不要在生产模式开`strict`,`strict`会深度检测vuex状态树,非常消耗性能
    strict: process.env.NODE_ENV !== 'production',
    // 全局的state
    state: {
      name: '小明',
      age: 20,
    },
    // 全局的mutations
    mutations: {
      changeName(state, data) {
        state.name = data;
      },
    },
    // 全局的actions
    actions: {
      changeNameAsync(store, data) {
        setTimeout(() => {
          store.commit('changeName', data);
        }, 2000);
      },
    },
    getters,
    modules,
  })
  ```
- src/store/getters.js
  ```js
  const getters = {
    // 在组件中是`this.$store.getters.roles`
    // getters 相当于`computed`, 这里的roles 依赖于user模块下的roles;当user模块下的roles改变,这个roles,也会改变
    roles: state => state.user.roles,
  };

  export default getters;
  ```
- src/store/modules/user.js
  ```js
  const state = {
    roles: ['无'],
    whiteList: ['无'],
    count: 48,
  }

  const mutations = {
    SET_ROUTES: (state, data) => {
      state.roles = data;
    },
    SET_WHITE_LIST: (state, data) => {
      state.whiteList = data;
    },
  }

  const actions = {
    setRoutes({commit, state}, val) {
      return new Promise((resolve, reject) => {
        let routes = ['admin'];
        setTimeout(() => {
          commit("SET_ROUTES", routes);
          resolve('SET_ROUTES_SUCCESS!');
        }, 1000);
      })
    },
    setWhiteList({commit, state}, val) {
      return new Promise((resolve, reject) => {
        let whiteList = ['/', '/404', '500'];
        setTimeout(() => {
          commit("SET_WHITE_LIST", whiteList);
          resolve('SET_WHITE_LIST_SUCCESS!');
        }, 1000);
      })
    },
  };

  export default {
    // 如果 namespaced 是 false, 那么此模块`user`的`state、mutations、actions`都会合并到`src/store/index.js`下的全局`state、mutations、actions`
    // 如果 namespaced 是 true, 那么在组件中获取此模块的`roles`, 需要这么写：this.$store.state.user.roles (多了`.user`)
    // 同理, namespaced 是 true, dispatch|commit 的格式也会改变, 例如`this.$store.dispatch('user/setRoutes');`
    namespaced: true,
    state,
    mutations,
    actions
  };
  ```