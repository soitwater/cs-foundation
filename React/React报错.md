# React报错
- 没必要所有的数据都放在redux,组件内部自己使用的还放在`state`,需要组件间通信的才是放在`state`
- `react-redux`的`reducer.js`更新完`state`后触发`update view`,但是相对应数据的组件在更新`state`之前就已经`update view`,导致数据`undefined`错误
