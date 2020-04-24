# Render 函数化组件
## 概念
- `functional: true` 表示` Render `组件无状态无实例（即没有data 和 this 上下文），这样的 Render 组件开销更小（因为是块级作用域）  
- `Render 函数组件`，提供第二个参数（一般命名 context）来表示临时上下文，以表示：data，props，slots，children，parent  
- 注意改写：
  * `this.level = context.props.level`

