# computed与watch与methods

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

## 区别

区别|形式|本质|场景|写法|位置  
-|-|-|-|-|-
methods|function|function|逻辑方法处理|null|null
computed|function|data|多个数据影响一个数据|null|本质是 Vue 实例的 data 对象，用于 {{}} 或 v-bind
watch|function|function|一个数据影响多个数据|null|null

## 参考
- [vue中methods、watch、computed之间的差别对比以及适用场景](https://www.cnblogs.com/widgetbox/p/8954162.html)
- [vue心得系列1——methods，computed，watch的区别](https://blog.csdn.net/u013344993/article/details/81985438)