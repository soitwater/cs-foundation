# Vue面试题

## Vue优点
- 双向数据绑定：视图 / 数据的分离
- 组件化
- 虚拟DOM：比操作原生dom更快

## 生命周期
- 8个

## 为什么vue组件中data必须是一个函数？
- 防止组件复用时，在A处修改了`data`后，B处的`data`也被意外修改
- 把`data`写成函数，使用返回对象的函数时，由于每次返回的都是一个新对象（Object的实例），引用地址不同，则不会出现这个问题

## v-if和v-show有什么区别
- 开销
  * `display:none;` & `opacity: 0;`

## computed和watch的区别
- 略

## `$nextTick`是什么？
- 略

## v-for key的作用
- key属性的类型只能为 string或者number类型，用于标识每个节点
- 它的作用体现在`VDom`算法中，在新旧nodes对比时辨识VNodes。如果不使用 key，Vue会使用一种最大限度减少动态元素并且尽可能的尝试修复/再利用相同类型元素的算法。使用key，它会基于key的变化重新排列元素顺序，并且会移除 key 不存在的元素。