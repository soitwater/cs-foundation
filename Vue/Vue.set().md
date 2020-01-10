# Vue.set()

## 应用场景
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

## 参考
- [官网](https://cn.vuejs.org/v2/guide/list.html#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
- [官网](https://cn.vuejs.org/v2/api/#Vue-set)