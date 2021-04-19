# vue3

## 安装
### 删除旧版本的vue
```c
npm uninstall vue-cli -g
yarn global remove vue-cli
```

### 安装最新的vue全家桶
```
npm install -g @vue/cli
npm install vuex@next --save
npm install vue-router@next --save
```

## Non-prop属性
- 父组件给子组件传值,但子组件并未接收,那么所传值会原封不动出现在子组件的最外层dom元素上
- 例如
  ```html
  <!-- 父组件：传递了msg属性给子组件HelloWorld -->
  <template>
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </template>
  ```
  ```js
  // 子组件不接收
  <template>
    <div class="hello"></div>
  </template>
  export default {
    name: 'HelloWorld',
    // props: {}
  }

  // 那么子组件的dom元素上会
  <div class="hello" msg="Welcome to Your Vue.js App"></div>
  ```
- 若子组件不想接收`Non-prop`属性，则：
  ```js
  export default {
    // 新增这一属性, 则子组件的dom元素上不会展示那些未接收的props属性
    inheritAttrs: false,
    name: 'HelloWorld',
    // props: {}
  }
  ```
- 如果子组件`template`下有多个根节点,此时`Non-prop`不会生效,除非添加`v-bind="$attrs"`,
  ```html
  <template>
     <!--只拿父组件传入Non-prop中的msg-->
     <div :msg="$attrs.msg">节点</div>
     <!--父组件传入的Non-prop都放入这里-->
     <div v-bind="$attrs">节点</div>
     <div>节点</div>
  </template>
  ```
- 子组件还可以通过`this.$attrs`直接获取`Non-prop`属性(并非只能在`<template>`中使用)
  

## props
- props 还可以写成数组的形式：`props: ['age', 'name', 'num']`
- 所传值可以限制的条件
  * type
  * required
  * default
  * validator 校验
- 如何修改父组件传给子组件的值
  * 父组件传给子组件的`props`是不允许修改, 若想修改, 可以
    ```js
    // 子组件
    export default {
      props: ['count'],
      data() {
        return {
          // 操作 this.myCount, 而非 this.count
          count: this.myCount,
        }
      }
    }
    ```
  * 也可以`this.$root.$emit`

## emits
- 在子组件
  ```js
  export default {
    // 声明子组件会向外触发哪些事件, 写不写无所谓;但如果在emits中使用了,但没有使用,那么会`console.warn`
    // 可以是数组, 其元素就是方法名
    // 也可以是对象, 属性值是方法
    emits: ['add', 'minus'],
    emits: {
      add: (val) => {
        // 若val > 0, 同意emit`add`事件; 
        // 若val <= 0, 则依旧会emit`add`事件,但是会在控制后台`console.warn`
        return val > 0;
      }
    }
  }
  ```

## modelValue
- 父子组件中若存在一个双向绑定的数据,那么可以用`modelValue`简写
- 在父组件中,
  ```html
  <template>
    <!--若不主动声明appVal,那么就默认是modelValue-->
    <!--写2个v-model也可以-->
    <HelloWorld v-model:appVal="appCount" v-model:tesVal="tesCount" />
  </template>

  <script>
  import HelloWorld from './components/HelloWorld.vue'

  export default {
    components: {
      HelloWorld,
    },
    data() {
      return {
        appCount: 1,
        tesCount: 1,
      }
    },
  }
  </script>
  ```
- 在子组件中,
  ```html
  <template>
    <div class="hello" @click="handleAppValClick">{{appVal}}</div>
    <div class="hello" @click="handleTesValClick">{{tesVal}}</div>
  </template>

  <script>
  export default {
    // 默认这里是写`modelValue`
    props: ['appVal', 'tesVal'],
    data() {
      return {}
    },
    methods: {
      handleAppValClick() {
        this.$emit('update:appVal', this.appVal + 1);
      },
      handleTesValClick() {
        this.$emit('update:tesVal', this.tesVal + 1);
      }
    }
  }
  </script>
  ```

## modelModifiers
- 自定义修饰符,默认是......,注意命名格式......
- 父组件
  ```html
  <template>
    <!--默认的修饰符是modelModifiers-->
    <HelloWorld v-model:appVal="appCount" v-model:tesVal.uppercase="tesCount" />
  </template>

  <script>
  import HelloWorld from './components/HelloWorld.vue';

  export default {
    components: {
      HelloWorld,
    },
    data() {
      return {
        appCount: 1,
        tesCount: 'a',
      }
    },
    methods: {}
  }
  </script>
  ```
- 子组件
  ```html
  <template>
    <div class="hello" @click="handleAppValClick">{{appVal}}</div>
    <div class="hello" @click="handleTesValClick">{{tesVal}}</div>
  </template>

  <script>
  export default {
    props: {
      appVal: {},
      tesVal: {},
      // 默认修饰符是modelModifiers
      tesValModifiers: {
        default: () => ({})
      }
    },
    data() {
      return {
      }
    },
    methods: {
      handleAppValClick() {
        let newVal = this.appVal + 1;
        this.$emit('update:appVal', newVal);
      },
      handleTesValClick() {
        let newVal = this.tesVal + 'b';
        // 如果存在tesVal的自定义修饰符中有uppercase, 那么就......
        if (this.tesValModifiers.uppercase) newVal = newVal.toUpperCase();
        this.$emit('update:tesVal', newVal);
      }
    }
  }
  </script>
  ```

## `this.$options`
- 获取定义在data外的数据和方法
  ```html
  <template>
    <div></div>
  </template>

  <script>
  export default {
    fn: function () {
      console.log('1628', 123);
      return '函数fn';
    },
    mounted() {
      console.log('1626', this.$options);
      console.log('1627', this.$options.fn());
    }
  }
  </script>
  ```

## Teleport
- 场景：遮罩, 将某局部组件所控制的遮罩传送至`<body>`下
- 示例组件
  ```html
  <template>
    <div class="area">
      <button class="btn" @click='handleClick'>点击</button>
      <!--将teleport下的元素直接挂载到其他元素下; to的值会被执行document.querySelector -->
      <teleport to="body">
        <div class="mask" v-show="show"></div>
      </teleport>
    </div>
  </template>

  <script>
  export default {
    components: {},
    data() {
      return {
        show: false,
      }
    },
    methods: {
      handleClick() {
        this.show = !this.show;
      },
    }
  }
  </script>

  <style>
  .area {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    width: 200px;
    height: 200px;
    background-color: red;
  }
  .btn {
    z-index: 2;
  }
  .mask {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    background-color: rgba(128, 128, 128, 0.5);
    z-index: 1;
  }
  </style>
  ```

## Suspense
- 与`react`中相同含义, 让你可以`等待`目标代码加载，并且可以直接指定一个加载的界面(像是个`spinner`)，让它在用户等待的时候显示
- 例子
  ```html
  <!-- App.vue -->
  <template>
    <div id="app">
      <div v-if="error">{{ error }}</div>
      <Suspense v-else>
        <template #default>
          <HelloWorld />
        </template>
        <template #fallback>
          Loading...
        </template>
      </Suspense>
    </div>
  </template>

  <script>
  import HelloWorld from "./components/HelloWorld.vue";
  import { onErrorCaptured, ref } from "vue";

  export default {
    components: {
      HelloWorld,
    },
    setup() {
      const error = ref(null);
      onErrorCaptured(e => {
        error.value = e;
        // 不对错误进行拦截
        return true;
      });
      return { error };
    },
  }
  </script>
  ```
  ```html
  <template>
    <div>
      <h1>{{ user.name }}</h1>
      <h1>{{ user.age }}</h1>
    </div>
  </template>

  <script>
  export default {
    async setup() {
      const fetchUser = () => {
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve({ name: "Carlo", age: "20" });
            // reject("Error, fail!");
          }, 3000);
        });
      };

      const user = await fetchUser();
      return { user };
    },
  }
  </script>
  ```
  
## ref | reactive | readonly | toRefs 
```html
<template>
  <!--4. 不需要用name.value; 因为vue内部做了处理-->
  <div>name: {{name}}</div>
  <div>科目: {{course.title}}; 分数: {{course.score}};</div>
  <div>只读科目: {{readonlyCourse.title}}; 分数: {{readonlyCourse.score}};</div>
  <div>科目: {{toRefCourseTitle}}; 分数: {{toRefCourseScore}};</div>
</template>

<script>
// 1. ref 与 reactive 是将普通数据变成响应式数据(原理:通过proxy对数据进行封装,当数据变化时,触发模板等内容的更新)
// 1. ref 是处理基础类型数据; reactive 处理引用型数据
// 1. 用于替代vue2中的data语法
// 1. readonly(参数) 返回一个只读代理, 传入的参数是ref()对象或者reactive()对象, 对象内部嵌套的属性也是只读的
// 1. toRefs 可以让 reactive的proxy的对象数据中,解构出来的值,也变成 响应式的数据
import { ref, reactive, readonly, toRefs } from 'vue';

export default {
  // 0. created 之前执行(无法使用this, 因为当前实例未创建)
  setup(props, context) {
    // 2. proxy, 'dell'会变成proxy({value: 'dell'}), 不然基础类型不能被proxy
    let name = ref('dell');
    setTimeout(() => {
      // 3. 要操作name的值, 需要用name.value
      name.value = 'lee';
    }, 2000);

    // 5. reactive 是对`对象类型`数据的proxy响应式引用, 包括数组
    const course = reactive({
      title: '数学',
      score: '100',
    });
    setTimeout(() => {
      course.score = '120';
    }, 2000);

    // 6. readonly 传入的参数是ref()或者reactive()对象
    const readonlyCourse = readonly(reactive({ title: '语文', score: '100', }));
    // 6. 无法修改, 会被阻止
    setTimeout(() => {
      readonlyCourse.score = '140';
    }, 2000);

    const toRefCourse = reactive({ title: '英语', score: '100' });
    // 7. toRefs 是将 proxy({title: '英语'}) 转化为 { title: proxy({value: '英语'}) }
    let { title: toRefCourseTitle, score: toRefCourseScore } = toRefs(toRefCourse);
    setTimeout(() => {
      // 7. 使用 `toRefCourseScore = '160';` 无效, 打印下  `console.log('1732', toRefCourseScore)`就知道原因了
      toRefCourse.score = '160';
    }, 2000);

    return {
      name,
      course,
      readonlyCourse,
      toRefCourseTitle,
      toRefCourseScore,
    }
  }
}
</script>
```

## toRef | context
### toRef的使用
```html
<template>
  <div>name: {{ name}}</div>
  <div>age: {{ age}}</div>
  <div>money: {{ money}}</div>
</template>

<script>
import { reactive, toRefs, toRef } from 'vue';

export default {

  setup(props, context) {
    const data = reactive({ name: 'dell' });
    // 1. name可以被会获取,因为data中有name键
    const { name } = toRefs(data);
    // 1. age不能被获取,因为data中没有age键,toRefs也不会自动为data创建age键
    const { age } = toRefs(data);
    // 1. money能获取, toRef表示尝试在data中获取money键;若money不存在,会创建money键
    const money = toRef(data, 'money');

    return {
      name, age, money
    }
  }
}
</script>
```
### context
- 父组件
  ```html
  <template>
    <!--3. -->
    <HelloWorld @change-val="handleChange">
      <span>父组件</span>
    </HelloWorld>
  </template>

  <script>
  import HelloWorld from './components/HelloWorld.vue';

  export default {
    components: {
      HelloWorld,
    },
    methods: {
      handleChange() {
        alert('on-change-val');
      }
    }
  }
  </script>
  ```
- 子组件
```html
<template>
  <!--3. -->
  <div @click="handleClick">子组件</div>
</template>

<script>
import { h } from 'vue';
export default {
  setup(props, context) {
    const { attrs, slots, emit } = context;
    // 1. attrs是父组件传入的none-props属性
    console.log(attrs);
    // 1. slots是父组件, slots.default()返回的是一个virtualDOM
    console.log(slots);
    // 1. 
    console.log(emit);
    // 2. 如果setup()返回的不是对象,而是一个render函数,那么该组件的`<template>`失效,使用的是render()函数
    // return () => h('div', {}, ['123456']);
    // return () => h('div', {}, slots.default());

    // 3. 效果等同于this.$slots.emit('change-val)
    function handleClick() {
      emit('change-val');
    }

    // 3. 
    return {
      handleClick
    }
  }
}
</script>
```

## computed
```html
<template>
  <div @click='addCount'>count: {{count}}</div>
  <div>countAddFive: {{countAddFive}}</div>
  <div>countAddFiveByGetSet: {{countAddFiveByGetSet}}</div>
</template>

<script>
// 1. computed
import { ref, computed } from 'vue'

const countRelativeEffect = () => {
  let count = ref(0);
  const addCount = () => { count.value++; };
  // 1. computed 接收一个回调函数
  const countAddFive = computed(() => count.value + 5);
  // 2. computed 还能接收一个[[GET]] [[SET]]
  let countAddFiveByGetSet = computed({
    // 2. 获取countAddFiveByGetSet 时使用这一get 回调
    get: () => count.value + 5,
    // 2. [[SET]] 不需要加return; 表示设置countAddFiveByGetSet时将执行的回调
    set: () => { count.value = 10 },
  });
  setTimeout(() => {
    // 2. 此时count === 10, countAddFive === 15, countAddFiveByGetSet === 15
    countAddFiveByGetSet.value = 100;
  }, 2000);

  return { count, countAddFive, countAddFiveByGetSet, addCount };
};

export default {
  setup(props, context) {
    const { count, countAddFive, countAddFiveByGetSet, addCount } = countRelativeEffect();
    return { count, countAddFive, countAddFiveByGetSet, addCount };
  }
}
</script>
```

## watch | watchEffect
```html
<template>
  <input type="text" v-model='name' />
  <div>name: {{name}}</div>
  <div>watchName: {{watchName}}</div>
  <div>watchNameObj: {{watchNameObj}}</div>
  <div>name: {{name}}</div>
</template>

<script>
// 1. watch, watchEffect
import { ref, reactive, watch, watchEffect, toRefs } from 'vue'

const nameRelativeEffect = () => {
  let nameObj = reactive({ name: '' });
  let { name } = toRefs(nameObj);
  let watchName = ref('');
  let watchNameObj = ref('');
  // 1. 懒惰性: 首次页面渲染完成,声明变量name 的时候不执行watch 的回调
  watch(name, (newVal, oldVal) => { watchName.value = `new(${newVal}), old(${oldVal})`; });
  // 2. 监听nameObj的name, 需要写成`() => nameObj.name`, 而不是`nameObj.name`
  watch(() => nameObj.name, (newVal, oldVal) => { watchNameObj.value = `new(${newVal}), old(${oldVal})`; });
  // 3. watch 还可以监听一个数组, 第一个参数是个数组, 第二个参数是个回调函数, 回调函数的参数是2个数组
  watch([name, () => nameObj.name], ([newName, oldName], [newNameobjName, oldNameobjName]) => {
    console.log('watch 监听数组', [newName, oldName], [newNameobjName, oldNameobjName])
  }, {
    // 6. 令watch失去惰性,进入页面时即立即执行,和`watchEffect`一样
    immediate: true,
  });
  // 4. watchEffect 接收的参数少于 watch, 只需要传递一个回调函数
  // 4. watchEffect 不能拿到之前的`值`,只能拿到数据的新值
  // 4. watchEffect不是惰性执行的, 页面加载的第一次就会执行(immediate)
  // 4. watchEffect 会自动去找回调函数中的哪些参数存在对外部的依赖,自动监听这些对外部依赖
  const stopWatchEffect = watchEffect(() => {
    console.log('watchEffect: ', name.value, nameObj.name);
  });
  // 5. 5秒后,清除watchEffect,不再进行监听
  // 5. watch的停止方式同理, 将watchEffect|watch 赋值给一变量, 执行`变量()`即可
  setTimeout(() => {
    stopWatchEffect();
  }, 5000);
  return { name, watchName, watchNameObj };
};

export default {

  setup(props, context) {
    const { name, watchName, watchNameObj } = nameRelativeEffect();
    return { name, watchName, watchNameObj };
  }
}
</script>
```

## 新生命周期
```html
<template>
  <div>你好,{{name}}!</div>
</template>

<script>
import {
  onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted,
  onRenderTracked, onRenderTriggered, ref
} from 'vue'
export default {
  setup(props, context) {
    const name = ref('小明');
    setTimeout(() => { name.value = '小红' }, 3000);

    // Composition API 中没有 created|beforeCreated
    onBeforeMount(() => console.log('lifecycle: onBeforeMount'));
    onMounted(() => console.log('lifecycle: onMounted'));
    onBeforeUpdate(() => console.log('lifecycle: onBeforeUpdate'));
    onUpdated(() => console.log('lifecycle: onUpdated'));
    onBeforeUnmount(() => console.log('lifecycle: onBeforeUnmount'));
    onUnmounted(() => console.log('lifecycle: onUnmounted'));
    // 当页面渲染后,vue会重新收集响应式依赖;页面刚进入时, 会触发onRenderTracked
    onRenderTracked(() => console.log('lifecycle: onRenderTracked'));
    // 当页面冲新渲染之后触发onRenderTracked; 每次触发 onRenderTriggered 之后都会触发 onRenderTracked
    onRenderTriggered(() => console.log('lifecycle: onRenderTriggered'));

    return { name };
  }
}
</script>
```

## provide | inject
- 在父组件
  ```html
  <template>
    <HelloWorld></HelloWorld>
  </template>

  <script>
  import HelloWorld from './components/HelloWorld.vue';

  // 1. 引入
  import { ref, provide, readonly } from 'vue';



  const nameRelativeEffect = () => {
    let name = ref('小明');
    // 1. 为所有的子孙组件传递数据`obj`
    // 1. provide 在 setup() 下的写法
    // 1. readonly 限制子孙组件不能修改参数name
    provide('name', readonly(name));
    provide('changeName', function (val) {
      console.log('进入 provide', val)
      name.value = val;
    });
  }
  export default {
    components: { HelloWorld },

    // 2. 可以是 obj, 也可以是返回 obj 的函数
    // provide: {
    //   name: '小明',
    // },

    setup(props, context) {
      nameRelativeEffect();
      return {};
    }
  }
  </script>
  ```
- 在子孙组件
  ```html
  <template>
    <div @click='handleClick'>{{name}}</div>
  </template>

  <script>
  import { inject } from 'vue';
  export default {
    // 2. inject 接收 provide 传过来的值
    // 2. inject 也可以简单写成 --> inject:['name', 'changeName']
    // 2. 如果inject要写在 export default {} 下,那就这么写; 注意可以有`from|default`两个属性
    // inject: {
    //   name: 'name',
    //   changeName: {
    //     from: 'changeName',
    //     default: () => { },
    //   }
    // },
    setup(props, context) {
      // 3. inject 在 setup() 中的写法
      const name = inject('name');
      const changeName = inject('changeName');
      // 3. inject 接收的参数应该在 provide 那里修改,这才符合单向数据流的要求
      function handleClick() {
        changeName(name.value === '小明' ? '小红' : '小明');
      }
      return { name, handleClick };
    }
  }
  </script>
  ```

## vue3中`this.$refs[key]`的新写法
```html
<template>
  <div ref="hello">你好</div>
</template>

<script>
import { ref, provide, readonly } from 'vue';

export default {
  mounted() {
    // <div ref="hello">你好</div>
    console.log(this.hello);
  },
  setup(props, context) {
    // 1. vue2.x中的 this.$refs[key] 的写法变成了下面这种
    // 1. 条件一：ref 传值为 null
    // 1. 条件二：变量名 hello 与 <div ref="hello">你好</div> 中的`ref="hello"`的 hello 相同
    const hello = ref(null);
    return { hello };
  }
}
</script>
```

## mixin
- 在vue3中不再推荐使用`mixin`了,因为有更好用的`composition api`;
- 示例
  ```js
  // src/main.js
  import { createApp } from 'vue'
  import App from './App.vue'
  import router from './router'
  import store from './store'
  import mitt from 'mitt'
  import myMixin from './mixin.js';

  const app = createApp(App);
  app.use(store).use(router);
  // 4. 全局mixin
  app.use(myMixin); 
  app.mount('#app');

  app.config.globalProperties.$emitter = mitt();
  // 5. 自定义 mixin 与 this.$options(自定义属性的)优先级
  app.config.optionMergeStrategies.optionNum = (mixinVal, appVal) => {
    console.log('自定义mixin与this.$options[key] 的优先级, 令其相反')
    return mixinVal || appVal;
  }
  ```
  ```js
  // src/mixin.js
  // 全局 mixin, 一般不推荐使用, 因为难维护 
  export default {
    install(Vue) {
      Vue.mixin({
        data() {
          return {
            globalMixinCount: '全局mixin的值'
          }
        }
      })
    }
  }
  ```
  ```js
  // Home.vue
  <template>
    <!-- 1. 当组件内已经有 number 了, 则不使用 mixin 里的number, 而是组件自己的 number（组件内部优先级更高）-->
    <!-- 1. 输出1-->
    <div>{{number}}</div>
    <div @click='handleClick'>click me</div>
    <!-- 4. 全局mixin -->
    <HelloWorld />
  </template>

  <script>
  import HelloWorld from '../components/HelloWorld.vue';

  const myMixin = {
    // 5. mixin中的optionNum
    optionNum: 98,
    data() {
      return {
        number: 2,
      }
    },
    mounted() {
      console.log('混入')
    },
    methods: {
      handleClick() {
        console.log('点击:mymixin');
      }
    },
  };

  export default {
    // 0. 混入
    // 0. 这里的mixin是局部的, myMixin 只在当前组件有效, 无法在当前组件之外的其他组件(含子组件)使用
    mixins: [myMixin],
    // 5. 当前实例的数字
    optionNum: 99,
    components: {
      HelloWorld,
    },
    // 1. this.$options.number (自定义属性) 的优先级也比 mixin 高
    number: 0,
    data() {
      return {
        number: 1
      }
    },
    mounted() {
      // 2. 先执行 mymixin 内的生命周期函数,再执行组件内的 生命周期函数
      console.log('组件');
    },
    created() {
      console.log('this.$options.optionNum 与 mixin中的optionNum 的优先级', this.$options.optionNum);
    },
    methods: {
      // 3. 同组件的`data`, 组件内的`methods`会覆盖`mymixin`里的`methods`
      handleClick() {
        console.log('点击:组件');
      }
    },
  }
  </script>
  ```
  ```js
  // src/components/HelloWorld.vue
  <template>
    <div>子组件: {{globalMixinCount}}</div>
  </template>
  ```

## 自定义指令
- src/main.js
  ```js
  import { createApp } from 'vue'
  import App from './App.vue'
  import router from './router'
  import store from './store'
  import mitt from 'mitt'
  import {consoleLog} from "./directive.js"

  const app = createApp(App);
  app.use(store).use(router);

  // 1. 全局指令
  app.directive('console-log', consoleLog);
  app.mount('#app');
  app.config.globalProperties.$emitter = mitt();
  ```
- src/directive.js
  ```js
  export const consoleLog = {
    beforeMount() {
      console.log('自定义全局指令v-console beforeMount');
    },
    mounted(el) {
      console.log('自定义全局指令v-console mounted');
    },
    beforeUpdate() {
      console.log('自定义全局指令v-console beforeUpdate');
    },
    updated() {
      console.log('自定义全局指令v-console updated');
    },
    beforeUnmount() {
      console.log('自定义全局指令v-console beforeUnmount');
    },
    unmounted() {
      console.log('自定义全局指令v-console unmounted');
    },
  }
  ```
- src/views/Home.vue
  ```html
  <template>
    <!-- 1. -->
    <div v-console-log>全局自定义指令v-console-log</div>
    <!-- 2. -->
    <div v-part:myname.foo.bar="{key: '???'}" id='v-part-div'>局部自定义指令v-part</div>
  </template>

  <script>
  export default {
    directives: {
      part: {
        mounted(el, binding, vnode) {
          console.log('el表示所绑定的元素：', el);
          // 2.
          console.log('局部指令', binding);
          console.log('局部指令: 值value, 即v-part=""中等号后面的内容 ', binding.value);
          console.log('局部指令: 更新前的旧值 ', binding.oldValue);
          console.log('局部指令: 传入的参数, 如 v-part:myname= 中的 myname ', binding.arg);
          console.log('局部指令: 修饰符，如 v-part:myname.foo.bar 中的 .foo.bar, 打印为 {bar:true,foo:true}', binding.modifiers);
          console.log('局部指令: vnode', Object.keys(vnode))
        }
      }
    },
    setup() {
      return {
        handleClick: () => { console.log('click', new Date()) },
      }
    }
  }
  </script>
  ```

## 插件
- 向`Vue`添加`全局`级功能
- src/main.js
  ```js
  import { createApp } from 'vue'
  import App from './App.vue'
  import router from './router'
  import store from './store'
  import mitt from 'mitt'
  import { myPlugin } from './plugins.js'


  const app = createApp(App);
  app.use(store).use(router);

  // 1. 插件
  app.use(myPlugin, {name: '小明'});

  app.mount('#app');

  app.config.globalProperties.$emitter = mitt();
  ```
- src/plugins.js
  ```js
  export const myPlugin = {
    install(app, options) {
      // 1. app 表示当前vue实例
      console.log('插件', app);
      // 1. 表示 app.use(myPlugin, {name: '小明'}); 时传入的 {name: '小明'}
      console.log('插件', options);
      // 2. 在全局vue声明一个变量name
      app.provide('name', '全局插件-provide：name的值');
      // 3. 可以在全局插件上, 定义全局变量, 定义全局指令, 混入组件选项, 定义全局方法
      app.mixin({
        mounted() {
          console.log('全局插件-mixin： mounted');
        }
      });
      app.directive('focus', {
        mounted(el) {
          console.log('全局插件-指令: focus');
        }
      });
      app.config.globalProperties.$sayHello = '全局插件-全局属性: hello world!';
    }
  };
  ```
- src/views/Home.vue
  ```html
  <template>
    <div v-focus>{{name}}</div>
  </template>

  <script>

  export default {
    // 2. 接收全局插件的变量name
    inject: ['name'],
    mounted() {
      console.log('组件内mounted时获取', this.$sayHello);
    },
  }
  </script>
  ```


## 待定render
```js
import { h } from 'vue';

// 普通组件
export const ComponentA = {
  render: function() {
    const text = this.type + "->" + this.name;
    return h(
      // 标签
      'p',
      // 标签内属性
      {},
      // 子元素
      text
    );
  },
  props: {
    type: { 
      type: String, 
      required: true 
    },
    name: { 
      type: String, 
      required: true 
    },
  }  
}

// 渲染多个 vnode
export const ComponentB = {
  render: function() { 
    // 'text' 即 'domProps'
    let childP = { render () { return h ("div", "text子元素") }};
    
    return h ( 
      "div",
      {},
      [h(childP), h(childP)]
    ) 
  }, 
};


// slot 的深复制
export const ComponentC = {
  render: function() {
    function cloneVNode (vnode) { // 克隆 slot 节点的方法
      // 递归遍历子节点，并克隆
      const clonedChildren = vnode.children && vnode.children.map(vnode => cloneVNode(vnode))

      const cloned = h(vnode.tag, vnode.data, clonedChildren);
      console.log('vnode:\n',vnode);
      cloned.text = vnode.text;
      cloned.isComment = vnode.isComment;
      cloned.componentOptions = vnode.componentOptions;
      cloned.elm = vnode.elm;
      cloned.context = vnode.context;
      cloned.ns = vnode.ns;
      cloned.isStatic = vnode.isStatic;
      cloned.key = vnode.key;

      return cloned;
    } 
    const vNodes = this.$slots.myslot;
    console.log('noted?', this.$slots, '\n', vNodes)
    const clonedVNodes = vNodes.map(vnode => cloneVNode(vnode))

    return h ( 
      "div",
      clonedVNodes
    ) 
  }, 
};
```

```html
<template>
  <div>hello, world</div>
  <ComponentA v-for="(item, index) of books" :key="index" :type="item.type" :name="item.name" />
  <ComponentB />
  <ComponentC>
    <template #myslot>
      <p>render function</p>
    </template>
  </ComponentC>
</template>

<script>
import { ComponentA, ComponentB, ComponentC } from '../components/HelloWorld.js';

export default {
  components: {
    ComponentA, ComponentB, ComponentC,
  },
  data() {
    return {
      books: [{ type: "小说", name: "西游记" }, { type: "诗词", name: "唐诗三百首" }],
    }
  },
}
</script>
```

## v-slot
- `<slot></slot>`无法绑定事件, 若需要绑定事件,就`<span @click="handleClick"> <slot></slot> </span>`


## v-router 新特性
```js
// src/router/index.js

// ......

const router = createRouter({
  // createWebHashHistory() 表示hash模式; 
  // createWebHistory() 表示非hash模式
  history: createWebHashHistory(),
  routes
})

// ......
```
```html
<!--在组件内-->
<template>
  <div @click="handleClick">click</div>
</template>

<script>
import { getCurrentInstance, onMounted } from 'vue';
import { useRouter, useRoute } from 'vue-router';

export default {
  setup(props, context) {
    const { proxy } = getCurrentInstance();
    // 获取 router
    const router = useRouter();
    // 获取 route
    const route = useRoute();
    onMounted(() => {
      // true
      console.log(proxy.$router === router);
      // {path, params, query...}
      console.log(route);
    });
  },
}
</script>
```

## getCurrentInstance
```js
import { getCurrentInstance } from 'vue';
// 获取当前组件实例
const instance = getCurrentInstance();

// 获取当前组件的上下文，下面两种方式都能获取到组件的上下文。
// 方式一，这种方式只能在开发环境下使用，生产环境下的ctx将访问不到
const { ctx }  = getCurrentInstance();  
// 方式二，此方法在开发环境以及生产环境下都能放到组件上下文对象（推荐）
const { proxy }  = getCurrentInstance();  
// ctx 中包含了组件中由ref和reactive创建的响应式数据对象,以及以下对象及方法;
proxy.$attrs
proxy.$data
proxy.$el
proxy.$emit
proxy.$forceUpdate
proxy.$nextTick
proxy.$options
proxy.$parent
proxy.$props
proxy.$refs
proxy.$root
proxy.$slots
proxy.$watch
```

## mitt
- 由于`Vue3.x`中删除了`on`和`off`，因此不能借助于一个单独的Vue实例来实现全局事件的发布和订阅与取消订阅（也就是跨组件通讯）
- 可以使用三方库`cnpm install -S mitt`(https://www.npmjs.com/package/mitt)
- 使用示例
  ```js
  // src/main.js
  import { createApp } from 'vue'
  import App from './App.vue'
  import router from './router'
  import store from './store'
  import mitt from 'mitt'

  const app = createApp(App);
  app.use(store).use(router).mount('#app');
  // 添加
  app.config.globalProperties.$emitter = mitt();
  ```
  ```html
  <!-- 组件内 -->
  <template>
    <div @click="handleClick">click</div>
  </template>

  <script>
  import { toRefs, getCurrentInstance } from 'vue';

  export default {
    setup(props, context) {
      // 获取实例
      const { proxy } = getCurrentInstance();
      // 注册事件
      proxy.$emitter.on('handle-click', (data) => alert(data));
      // 触发事件
      const handleClick = () => proxy.$emitter.emit('handle-click', Math.ceil(Math.random() * 100));
      return { handleClick };
    },
  }
  </script>
  ```

## defineComponent
- vue3 如果用`ts`，导出时候要用 `defineComponent`，这俩是配对的，为了类型的审查正确
- 示例
  ```ts
  <template>
    <li class="dropdown-option"></li>
  </template>

  <script lang="ts">
  import { defineComponent } from "vue";

  export default defineComponent({
    props: {},
    setup() {},
  });
  </script>
  ```

## PropType
```html
<template>
  <div>你好 {{user.name}}</div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";

export interface UserProps {
  isLogin: boolean;
  name?: string;
  id?: number;
}

export default defineComponent({
  props: {
    user: {
      type: Object as PropType<UserProps>,
      required: true,
    },
  },
});
</script>
```
- 用一句简单的话来说，就是为了类型推论，让我们在使用属性的时候获取更丰富的类型提示，
- 比如在这里我们定义了一个属性 list，使用 vue 默认的 Array，只能确定它是一个数组类型，不能确定数组里面的每一项到底是什么样子的。
- 你在 setup 中，看 props.list 就是一个any数组，
- 但是如果使用`PropType <ColumnProps[]>` 这个时候，props.list 就变成一个 ColumnProps 的数组，
- 你使用它的时候不论在 ts 中还是模版中都能获得类型的推断和自动补全等等。

## 查看某一元素或某一事件的类型的方式
- [文档](https://developer.mozilla.org/zh-CN/docs/Web/API)
- 在文档搜`HTMLInputElement`,查看元素
- 常用的Event事件对象类型
  ```js
  ClipboardEvent<T = Element> 剪贴板事件对象
  DragEvent<T = Element> 拖拽事件对象
  ChangeEvent<T = Element> Change 事件对象
  KeyboardEvent<T = Element> 键盘事件对象
  MouseEvent<T = Element> 鼠标事件对象
  TouchEvent<T = Element> 触摸事件对象
  WheelEvent<T = Element> 滚轮事件对象
  AnimationEvent<T = Element> 动画事件对象
  TransitionEvent<T = Element> 过渡事件对象
  ```