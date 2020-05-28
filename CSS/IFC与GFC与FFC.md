# IFC与GFC与FFC.md

## IFC
- Inline Formatting Contexts，也就是“内联格式化上下文”
- [参考](https://segmentfault.com/a/1190000017273573)
- 形成条件：`块级元素中仅包含内联级别元素`
- 特征：`解决元素垂直居中、多个文本元素行高不一致排列混乱`

## GFC（css3）
- GFC`(GridLayout Formatting Contexts)`直译为"网格布局格式化上下文"
- 当为一个元素设置display值为`grid`的时候，此元素将会获得一个独立的渲染区域

## FFC（css3）
- FFC`(Flex Formatting Contexts)`直译为"自适应格式化上下文"
- display值为`flex`或者`inline-flex`的元素将会生成自适应容器（flex container）