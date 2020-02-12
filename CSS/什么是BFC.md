# 什么是BFC

&nbsp;

## 前置知识
BFC即块级格式化上下文。一般CSS布局有普通流，float，绝对定位等，对于后两种，因为存在元素脱离了文档流，因此产生BFC.  

&nbsp;

## 定义
> 具有 BFC 特性的元素可以看作是隔离了的独立容器，  
> 容器里面的元素不会在布局上影响到外面的元素，  
> 并且 BFC 具有普通容器所没有的一些特性

&nbsp;

## BFC触发
* body 根元素
* 浮动元素：float 除 none 以外的值
* 绝对定位元素：position (absolute、fixed)
* display 为 inline-block、table-cells、flex（即行内元素中的块级元素）
* overflow 除了 visible 以外的值 (hidden、auto、scroll)

&nbsp;

## BFC特性
### 1. 垂直Margin重叠
如下的两个盒子的上下Margin Box将发生重叠
```html
<head>
div{
    width: 100px;
    height: 100px;
    background: red;
    margin: 100px;
}
</head>
<body>
    <div></div>
    <div></div>
</body>
```
若不想重叠，应将两个盒子放入不同BFC容器中
```html
<div class="container">
  <div></div>
</div>
<div class="container">
  <div></div>
</div>
```
```css
.container {
  overflow: hidden;
}
.container>div {
  width: 100px;
  height: 100px;
  background: red;
  margin: 100px;
}
```
### 2. 清除浮动
浮动元素: 外层盒子仅2px的高度
```html
<div style="border: 1px solid #000;">
  <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```
overflow:hidden;触发外层盒子的BFC
```html
<div style="border: 1px solid #000;overflow: hidden">
  <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```
### 3.两列布局
下盒子(非BFC)被上盒子(BFC)覆盖, 此时为下盒子添加 overflow: hidden; 则将实现两列布局
```html
<div style="height: 100%;width: 100px;float: left;background: red">我是一个左浮动的元素</div>
<div style="width: auto; height: 100%;background: #ccc">我是一个没有设置浮动, 也没有触发 BFC 元素，注意文字环绕注意文字环绕注意文字环绕</div>
```

&nbsp;

## 参考
[10 分钟理解 BFC 原理](https://zhuanlan.zhihu.com/p/25321647)