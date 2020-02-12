# 清除浮动之父子div的margin重叠

例子
```js
<!--若parent与child都设置margin，那么child并不会与parent有间隔，而是依然重叠-->
<div id="parent">
  <div id="child"></div>
</div>

<!--解决方法是：1.在child前添加一个div；2.设置parent与child的border；3.设置parent的margin-->
```

## 参考
- [父子div margin重叠问题](https://www.cnblogs.com/little-bug/p/6914535.html)