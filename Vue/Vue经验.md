# Vue经验

## 全局变量
- 在`main.js`中
  ```js
  Vue.prototype._global = {
    _a: 1
  }
  ```
- 在需要的组件内`this._global`即可