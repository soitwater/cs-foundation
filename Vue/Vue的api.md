# Vue的api

## computed 
- 虽然 methods 也可以实现计算，但 computed 根据依赖判断是否需要重新计算目标变量，性能更佳
- 适合场景：一个数据依赖于多个数据计算所得
- 代码
  ```html
  <template>
    <div>
      <div>{{addNum}}</div>
      <div @click="randomNum">click&nbsp;me</div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'Son',
    data () {
      return {
        "a": 10,
        "b": 20,
      }
    },
    computed: {
      addNum: {
        get: function () { // addNum 可以不出现在 data () {} 中
          return this.a + this.b
        },
        set: function (newNum) {
          this.a = this.b = newNum / 2
        }
      },
    },
    methods: {
      randomNum () {
        this.a = Math.random() * 100
        this.b = Math.random() * 100
      }
    }
  }
  </script>
  ```

## watch
适用于一个数据影响多个数据的场景  
一般优先考虑 computed，再考虑 watch  
store.js
```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)

const state = {
  student: {
    age: 21,
    grade: {
      math: 80,
      chinese: 80,
      english: 80
    }
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
son.vue
```html
<template>
  <div>
    <div>a&nbsp;{{a}}</div>
    <div>b&nbsp;{{b}}</div>
    <div @click="randomNum">click&nbsp;me</div>
  </div>
</template>

<script>
import store from "@/store/store.js"
export default {
  name: 'Son',
  data () {
    return {
      "a": 10,
      "b": 20,
      student: store.state.student // watch 监听的数据 必须出现在 data 对象中
    }
  },
  // 按这个模板写即可
  watch: {
    student: {
      handler: function (val, oldVal) {
        this.a = val.age + " 岁"
        this.b = val.grade.chinese + "/" + val.grade.math + "/" + val.grade.english
      },
      // 开销会变大，因为`student`下的每一属性都会被添加`监听`
      deep: true
    }
  },
  methods: {
    randomNum () {
      this.student.age = Math.ceil(Math.random() * 30)
      let grade = this.student.grade
      grade.chinese = getGrade()
      grade.math = getGrade() 
      grade.english = getGrade()
      function getGrade () {
        return Math.ceil(Math.random() * 100)
      }
    }
  }
}
</script>
```


## v-slot
- 匿名/具名插槽
  * 父页面
    ```html
    <todo-list> 
      <template v-slot:default>
        任意内容
        <p>我是匿名插槽 </p>
      </template>
    </todo-list>  
    ```
  * 子页面(即 todo-list)
    ```html
    <slot>我是默认值</slot>
    ```
  * v-slot:default 匿名插槽
  * v-slot:{name} 动态具名插槽
- 作用域插槽
  * 父页面
    ```html
    <HeaderItem :msg="item" v-for='(item, index) of headerMsg' :key='index'>
      <!--slotProps 可以随意命名-->
      <!--slotProps.label 表示的是子组件对应的`slot`上的标签属性 label-->
      <template v-slot:label='slotProps'>{{slotProps.label}}</template>
    </HeaderItem>

    <script>
    export default {
      data() {
        return {
          headerMsg: [
            { label: '今日重启(次)', num: '--', },
            { label: '本周重启(次)', num: '--', },
            { label: '本月重启(次)', num: '--', },
            { label: '本季度重启(次)', num: '--', },
            { label: '本年度重启(次)', num: '--', },
          ],
        }
      },
    }
    </script>
    ```
  * 子页面
    ```js
    <template>
      <div class="header-item">
        <slot name='label' :label="msg.label">文字信息</slot>
      </div>
    </template>

    <script>
    export default {
      props: {
        msg: {},
      }
    }
    </script>
    ```

## Render 
### 注意
- 易错：Render函数渲染子节点[]时，VNode 仅能使用一次，不能重复使用，  
  * 如错误写法: `let a = creatElement(...); render (h) {return h ("p", {}, [a, a])}`  
  * 同理, `render (h) {return h ("p", {}, [this.$slots.default, this.$slots.default])}`，也是错误的  
  * 注: v2.5.18 开始 vue 支持 vnode 复用了.

### 例子（Render）
- test1.vue
  ```html
  <template>
    <div id="test1">
      <render-temple
        v-for="(item, index) of tdATA" :key="index"
        :type="item.type" :name="item.name">
      </render-temple> 
    </div>
  </template>
  
  <script>
  import RenderTemple from "./renderTemple"
  export default {
    data () {
      return {
        tdATA: [{type:"小说",name:"西游记"},{type:"诗词",name:"唐诗三百首"}],
      }
    },
    components: {
      RenderTemple
    },
  }
  </script>
  ```
- renderTemple.js
  ```js
  export default {
    render : function (h) { 
      return h ( 
        "p",
        { 
          domProps: {
            innerHTML:this.type + "->" + this.name,
          }
        },
      ) 
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
  };
  ```

### 例子（渲染多个 VNode）
- test1.vue 同 例子（Render）  
- renderTemple.js
  ```js
  let childP = {
    render (h) {
      return h ("div", "text") // “text” 即 “domProps”
    }
  }
  export default {
    render : function (h) { 
      return h ( 
        "div",
        { 
          // domProps: { // 声明 donProps > innerHTML 将覆盖子元素
          //   innerHTML:this.type + "->" + this.name,
          // }
        },
        [h(childP), h(childP)]
      ) 
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
  };
  ```

### 例子（slot的深复制）
- test1.vue 同 例子(Render 与 Slot 混合)
- renderTemple.js:
  ```js
  export default {
    render : function (h) {
      // 克隆 slot 节点的方法
      function cloneVNode (vnode) {
        // 递归遍历子节点，并克隆
        const clonedChildren = vnode.children && vnode.children.map(vnode => cloneVNode(vnode));
  
        const cloned = h (vnode.tag, vnode.data, clonedChildren);
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
      const vNodes = this.$slots.default;
      const clonedVNodes = vNodes.map(vnode => cloneVNode(vnode));
  
      return h ( 
        "div",
        clonedVNodes
      );
    }, 
  };
  ```

### 例子(Render 与 Slot 混合)
- test1.vue
  ```html
  <template>
    <div id="test1">
      <render-temple :level="4"> 
        <p slot="myslot">render function</p> 
      </render-temple> 
    </div>
  </template>
  
  <script>
  import RenderTemple from "./renderTemple"
  
  export default {
    data () {
      return {
      }
    },
    methods: {
    },
    components: {
      RenderTemple
    },
  }
  </script>
  ```
- renderTemple.js
  ```js
  export default {
    render : function (createElement) { 
      // 支持简写 h
      return createElement( 
        'h' + this.level, // 标签
        { 
          // 数据对象
          'class': { 
            show:true, 
            hide:false, 
          }, 
          style:{ 
            background:'red', 
          }, 
          attrs:{ 
            name:'h-ex', 
            id:'h-id'
          }, 
          props:{ 
            myprops:true, 
          }, 
          on: { 
            click: function(event){ 
              console.log(event, this) 
            } 
          }, 
          nativeOn:{ 
            click:function(event) { 
              alert("nativeOn") 
            } 
          } 
        }, 
        [ 
          // 子元素
          this.$slots.myslot, // this.$slots 具体指 调用者(test1.vue)里的所有slots，this.$slots.myslot 指slots中的具名为“myslot”的slots，
          createElement('p', { 
            domProps:{ 
              innerHTML:'holle render'
            } 
          }) 
        ] 
      
        ) 
    }, 
    props: { 
      level: { 
        type: Number, 
        required: true 
      } 
    }  
  };
  ```
### Render 函数化组件
- `functional: true` 表示` Render `组件无状态无实例（即没有data 和 this 上下文），这样的 Render 组件开销更小（因为是块级作用域）  
- `Render 函数组件`，提供第二个参数（一般命名 context）来表示临时上下文，以表示：data，props，slots，children，parent  
- 注意改写：
  * `this.level = context.props.level`


### Render中实现VUE特性
#### v-if
```js
if (1) {
  return h ("ul", "text")
} else {
  return h ("li", "无")
}
``` 

#### v-for
```js
let vnodes = []
for () {
  vnodes.push(h("div", "text"))  
}
```

#### v-model
- 针对`<input>`元素
  ```html
  <template>
    <div id="test1">
      <render-temple></render-temple> 
    </div>
  </template>
  
  <script>
  import RenderTemple from "./renderTemple"
  export default {
    components: {
      RenderTemple
    },
  }
  </script>
  ```
  ```js
  export default {
    render : function (h) {
      let _this = this
      return h ( 
        "div", [
          h("input", {
            attr: {
              type: "text"
            },
            domProps: {
              value: this.value
            },
            on: {
              input (event) {
                _this.value = event.target.value
              }
            }
          }),
          h("p", "value: " + this.value)
        ]
      ) 
    }, 
    data () {
      return {
        value: "",
      }
    }
  }
  ```
- Render 后的组件
  ```html
  <div>
    <input v-model="value">
    <p>value:{{value}}</p>
  </div>
  ```

## 事件修饰符
| 修饰符   | 句柄                                             |
| -------- | ------------------------------------------------ |
| .stop    | event.stopPropagation()                          |
| .prevent | event.preventDefault()                           |
| .self    | if (event.target !== event.currentTarget) return |
| .enter   | if (event.keyCode !== 13) return                 |
| .ctrl    | if (!event.ctrlKey) return                       |

## 按键修饰符
| 修饰符                         | 前缀 |
| ------------------------------ | ---- |
| .capture                       | !    |
| .once                          | ~    |
| .capture.once 或 .capture.once | ~!   |
```js
// 对于事件修饰符，Vue 提供了特殊的前缀
 on: {
  "!click": this.dosomething
 }
```

## Vue.set()
### 举例 - vue在双向绑定对象类型数据时
- `ob`初始时是没有`txt`属性的，因此`v-model`失败
- 应该尝试一下`Vue.set(obj, key, value)`, 当不知道`obj`里需要定义什么属性，但又需要用到`obj`里定义的东西进行双向绑定时，可以使用

```js
<div id="app">
  <div>
    <input type="text" v-model="ob.txt">
  </div>
</div>

new Vue({
  el:'#app',
  data:{
    name:'张三',
    ob:{}
  },
})
```

## 指令  
- 以`v-time`将时间戳转换为相对本机时间的多久之前,为例子
  ```js
  // 一. 写一个Time的库								
  let Time = {								
    getUnix () { // 获取当前时间戳								
      let date = new Date()								
      return date.getTime()								
    },								
    getTodayUnix () { // 获取今天0点0分0秒的时间戳								
      let date = new Date()								
      date.setHours(0)								
      date.setMinutes(0)								
      date.setSeconds(0)								
      date.setMilliseconds(0)								
      return date.getTime()								
    },								
    getYearUnix () { // 获取今年1月1日0点0分0秒的时间戳								
      let date = new Date()								
      date.setMonth(0)								
      date.setDate(1)								
      date.setHours(0)								
      date.setMinutes(0)								
      date.setSeconds(0)								
      date.setMilliseconds(0)								
      return date.getTime()								
    },								
    getLastDate (time) { // 获取标准年月日								
      let date = new Date(time)								
      let month = date.getMonth() + 1 < 10 ? "0" + (date.getMonth() + 1) : date.getMonth() + 1								
      let day = date.getDate() < 10 ? "0" + date.getDate() : date.getDate()								
      return date.getFullYear() + "-" + month + "-" + day								
    },								
    getFormatTime (timestamp) { // 转换时间								
      let now = this.getUnix() // 当前时间戳								
      let today = this.getTodayUnix() // 今天0点时间戳								
      let year = this.getYearUnix() // 今年0点时间戳								
      let timer = (now - timestamp) / 1000 // 转换为秒级时间戳								
      let tip = ""								
      if (timer <= 0) {								
        tip = "刚刚"								
      } else if (Math.floor(timer / 60) <= 0) {								
        tip = "刚刚"								
      } else if (timer < 3600) {								
        tip = Math.floor(timer / 60) + "分钟前"								
      } else if (timer >= 3600 && (timestamp - today >= 0)) {								
        tip = Math.floor(timer / 3600) + "小时前"								
      } else if (timer / 86400 <= 31) {								
        tip = Math.ceil(timer / 86400) + "天前"								
      } else {								
          tip = this.getLastDate(timestamp)								
      }								
      return tip								
    },								
  }								
                  
  export {Time}								
  ```
  ```js
  // 二. 自定义指令					
  directives: {					
    "time": {					
      bind (el, binding) {					
        el.innerHTML = Time.getFormatTime(binding.value)					
        el.__timeout__ = setInterval(() => {					
          el.innerHTML = Time.getFormatTime(binding.value)  					
        }, 60000)					
      },					
      unbind () {					
        clearInterval(el.__timeout__)					
        delete el.__timeout__					
      }					
    }					
  },					
  ```
  ```js
  // 三. 使用自定义指令								
  v-time="timeNow"								
  v-time="timeBefore								
  timeNow: (new Date()).getTime(),								
  timeBefore: 1554988307968,								
  ```
