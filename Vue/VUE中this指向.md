# VUE中this指向
下面的代码会输出什么？
```js
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

var Component = Vue.extend({
  mixins: [myMixin],
  methods: {
    hello(){
      console.log('hello from options')
    }
  }
})

var component = new Component()
```
输出：hello from options  
当混入对象与组件数据同名时，优先组件数据

## 参考
- [混入](https://cn.vuejs.org/v2/guide/mixins.html)