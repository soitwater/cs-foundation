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

## 在 Vue 中，子组件为何不可以修改父组件传递的 Prop ?
- 因为vue设计是单向数据流，数据的流动方向只能是自上往下的方向；
- 易于监测数据的流动，出现了错误可以更加迅速的定位到错误发生的位置。
- 当子组件可以直接修改父组件的 prop 时，会导致其他用到此props的子组件的值也发生改变，导致数据混乱，以及难以追踪的bug。如果props是基础类型，不会造成其他影响，但也会是整个组件数据混乱。


## 如果修改了，Vue 是如何监控到属性的修改并给出警告的。
- 在initProps的时候，在defineReactive时通过判断是否在开发环境，如果是开发环境，会在触发set的时候判断是否此key是否处于updatingChildren中被修改，如果不是，说明此修改来自子组件，触发warning提示。
- 需要特别注意的是，当你从子组件修改的prop属于基础类型时会触发提示。 这种情况下，你是无法修改父组件的数据源的， 因为基础类型赋值时是值拷贝。你直接将另一个非基础类型（Object, array）赋值到此key时也会触发提示(但实际上不会影响父组件的数据源)， 当你修改object的属性时不会触发提示，并且会修改父组件数据源的数据。