# Render 中实现 VUE 特性
Render 中无法使用 **v-for**&nbsp;&nbsp;  **v-if**&nbsp;&nbsp;**v-model**以及事件修饰符，按键修饰符等，需要原生JS实现

## v-if
```js
if (1) {
  return h ("ul", "text")
} else {
  return h ("li", "无")
}
``` 

## v-for
```js
let vnodes = []
for () {
  vnodes.push(h("div", "text"))  
}
```

## v-model
针对 \<input\> 元素
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
Render 后的组件
```html
<div>
  <input v-model="value">
  <p>value:{{value}}</p>
</div>
```

## 事件修饰符
修饰符|句柄
-|-
.stop|event.stopPropagation()
.prevent|event.preventDefault()
.self|if (event.target !== event.currentTarget) return
.enter|if (event.keyCode !== 13) return
.ctrl|if (!event.ctrlKey) return

## 按键修饰符
修饰符|前缀
-|-
.capture|!
.once|~
.capture.once 或 .capture.once |~!
```js
// 对于事件修饰符，Vue 提供了特殊的前缀
 on: {
  "!click": this.dosomething
 }
```