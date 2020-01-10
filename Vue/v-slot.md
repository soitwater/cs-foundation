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
    <todo-list>
     <template v-slot:todo="slotProps" >
       {{slotProps.user.firstName}}
      </template> 
    </todo-list> 
    //slotProps 可以随意命名
    //slotProps 接取的是子组件的 props
    ```
  * 子页面
    ```js
    <slot name="todo" :user="user" :test="test">
      {{ user.lastName }}
    </slot> 
    data() {
        return {
          user:{
            lastName:"Zhang",
            firstName:"yue"
          },
          test:[1,2,3,4]
        }
      },
    // {{ user.lastName }}是默认数据  当父页面没有(="slotProps")时显示 Zhang
    // 上面显示的是：yue
    ```

## 参考
- [详解vue2.6插槽更新v-slot用法总结](https://www.jb51.net/article/157565.htm)