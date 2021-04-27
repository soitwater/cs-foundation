# Vue经验

## 全局变量
- 在`main.js`中
  ```js
  Vue.prototype._global = {
    _a: 1
  }
  ```
- 在需要的组件内`this._global`即可

## 为什么Vue中对mouseover的监听无效
```html
<!-- 加上 .native -->
<el-menu-item index="1" @mouseover.native="showUp">find Music</el-menu-item>
```