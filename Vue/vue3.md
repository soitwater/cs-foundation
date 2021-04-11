# vue3

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

## v-slot
- `<slot></slot>`无法绑定事件, 若需要绑定事件,就`<span @click="handleClick"> <slot></slot> </span>`
