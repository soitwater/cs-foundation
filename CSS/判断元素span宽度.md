# 判断元素span宽度

## 问题
```html
<div style=”width:400px;height:200px;”>
  <span style=”float:left;width:auto;height:100%;”>
           <i style=”position:absolute;float:left;width:100px;height:50px;”>hello</i>
  </span>
</div>
```
`<span/>` 的 width 是 0px, height = 200 px

## 解释
- 首先span不是块级元素，是不支持宽高的，但是style中有了个float：left；就使得span变成了块级元素支持宽高，height:100%;即为，200，宽度由内容撑开。
- 但是内容中的 i 是绝对定位，脱离了文档流，所以不占父级空间，所以span的width=0
