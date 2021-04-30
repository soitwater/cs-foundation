# v-slot
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

## 参考
- [详解vue2.6插槽更新v-slot用法总结](https://www.jb51.net/article/157565.htm)