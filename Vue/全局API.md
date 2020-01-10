# 全局API

## Vue.extend
- extend创建的是一个组件构造器，而不是一个具体的组件实例。
  * 使用
    ```js
    // 创建构造器
    var Profile = Vue.extend({
      template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
      data: function () {
        return {
          firstName: 'Walter',
          lastName: 'White',
          alias: 'Heisenberg'
        }
      }
    })
    // 创建 Profile 实例，并挂载到一个元素上。
    new Profile().$mount('#mount-point')
    ```
## Vue.mixins
- [参考](https://www.yuque.com/fe9/basic/ey2dim)
- 理解
  * 一段代码
    ```js
    const myMixin = {
      data() {
        return {
          message: 'this is mixin message'
        }
      },
      created() {
        console.log('mixin created')
      }
    }
    
    const vm = new Vue({
      el: '#app',
      mixins: [myMixin],
      data() {
        return {
          message: 'this is vue instance message'
        }
      },
      created() {
        console.log(this.message)
        // => Root Vue Instance
        console.log('vue instance created')
        // => created myMixin
        // => created Root Vue Instance
      }
    })
    ```
  * mixins是一个数组,其元素是mixin; mixin是一个对象,其内部有`data、created、methods`等等vue实例中拥有的所有属性
  * mixins 与 Vue Instance 合并时，会将 created 等钩子函数合并成数组，mixins 的钩子优先调用，当 data、methods 对象键值冲突时，组件会覆盖mixin
- mixins与extend的区别
  * 功能类似, 在同名key上, mixins会覆盖extend的; 但是在钩子函数调用上, extend是优先执行的。  
  * 即：data/methods优先级： component 覆盖 (mixins 覆盖 extend)  
  * 钩子函数优先级：不会覆盖, extend > mixins > component 
  * 示例代码
    ```js
    <template>
      <div id="jobtopic" class="job-topic"></div>
    </template>
    
    <script>
    import Vue from "vue"
    let myExtend = Vue.extend({
      data() {return {name: "myExtend"}},
      methods: {say() {console.log("method in myExtend")}},
      created() {console.log("created in myExtend")}
    })
    new myExtend().$mount("#myextend") // 在/public/index.html(即html模板里应该有DOM元素的id是myextend)
    let myMixin = {
      data() {return {name: 'myMixin', age: '16'}},
      methods: {say() {console.log("method in myMixin")}},
      created() {console.log("created in myMixin")}
    } 
    export default {
      mixins: [myMixin],
      data() {return {name: 'myComponent'}},
      methods: {say() {console.log("method in myComponent")}},
      mounted() {console.log(this.$data);this.say()},
      created() {console.log("created in myComponent")}
    }
    </script>
    ```