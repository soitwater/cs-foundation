# flex弹性盒子

## flex属性有哪些？各自属性取值有哪些？每个属性的作用？
  ```css
  display: flex / inline-flex
  flex-direction: row | row-reverse | column | column-reverse;
  flex-wrap: nowrap | wrap | wrap-reverse;
  flex-flow: <‘flex-direction’> || <‘flex-wrap’>
  justify-content: flex-start | flex-end | center | space-between | space-around |   space-evenly;
  align-items: flex-start | flex-end | center | baseline | stretch;
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;/*交叉轴上的多行(若flex子项只有一行，则该属性无效)*/
  ```
  ```css
  order: <integer>;  /* 默认值是 0, 可以是 -1 */
  flex-grow：非负整数; /*表示所占空间（相对于剩余空间的利用）*/
  flex-shrink：1;/*（默认1）主要处理当flex容器空间不足时候，单个元素的收缩比例。*/
  flex-basis: <length> | auto; /* default auto */
  flex: 0 1 auto (默认); /*是 flex-grow、flex-shrink、flex-basis 三个属性的缩写*/
  align-self: auto | flex-start | flex-end | center | baseline | stretch; /*align-self 属性允许某个单独的 flex 项覆盖默认的对齐方式*/
  ```

## flex-grow
>- flow-grow 还不如直接 width 以及 height 用 百分比
>- flex-grow 会覆盖属性 width; height;
>- flex-grow属性中的grow是扩展的意思，扩展的就是flex子项所占据的宽度，扩展所侵占的空间就是除去元素外的剩余的空白间隙。
>- 具体的扩展比较复杂。在展开之前，我们先看下语法。
>  * flex-grow: \<number\>; /* 数值，可以是小数，默认值是 0 */
>  * flex-grow不支持负值，默认值是0，表示不占用剩余的空白间隙扩展自己的宽度。如果flex-grow大于0，则flex容器剩余空间的分配就会发生，具体规则如下：
>    - 所有剩余空间总量是1。
>      * 如果只有一个flex子项设置了flex-grow属性值：
>      * 如果flex-grow值小于1，则扩展的空间就总剩余空间和这个比例的计算值。
>      * 如果flex-grow值大于1，则独享所有剩余空间。
>    - 如果有多个flex设置了flex-grow属性值：
>      * 如果flex-grow值总和小于1，则每个子项扩展的空间就总剩余空间和当前元素设置的flex-grow比例的计算值。
>      * 如果flex-grow值总和大于1，则所有剩余空间被利用，分配比例就是flex-grow属性值的比例。例如所有的flex子项都设置flex-grow:1，则表示剩余空白间隙大家等分，如果设置的flex-grow比例是1:2:1，则中间的flex子项占据一半的空白间隙，剩下的前后两个元素等分。

## flex-shrink
> - flex-shrink不支持负值，默认值是1，也就是默认所有的flex子项都会收缩。如果设置为0，则表示不收缩，保持原始的fit-content宽度。
> - flex-shrink的内核跟flex-grow很神似，flex-grow是空间足够时候如何利用空间，flex-shrink则是空间不足时候如何收缩腾出空间。
> - 如果只有一个flex子项设置了flex-shrink：
>   * flex-shrink值小于1，则收缩的尺寸不完全，会有一部分内容溢出flex容器。
>   * flex-shrink值大于等于1，则收缩完全，正好填满flex容器。
> - 如果多个flex子项设置了flex-shrink：
>   * flex-shrink值的总和小于1，则收缩的尺寸不完全，每个元素收缩尺寸占“完全收缩的尺寸”的比例就是设置的flex-shrink的值。
>   * flex-shrink值的总和大于1，则收缩完全，每个元素收缩尺寸的比例和flex-shrink值的比例一样。

## flex-basis
> - flex-basis定义了在分配剩余空间之前元素的默认大小。相当于对浏览器提前告知目标DOM节点需要占据多大的空间。
> - 语法如下：
>   * flex-basis: \<length\> | auto; /* 默认值是 auto */
>   * 默认值是auto，就是自动。有设置width则占据空间就是width，没有设置就按内容宽度来。
>   * 如果同时设置width和flex-basis，就渲染表现来看，会忽略width。flex顾名思义就是弹性的意思，因此，实际上不建议对flex子项使用width属性，因为不够弹性。
>   * 当剩余空间不足的时候，flex子项的实际宽度并通常不是设置的flex-basis尺寸，因为flex布局剩余空间不足的时候默认会收缩。

## flex 
- flex默认值等同于flex:0 1 auto；
- flex:none等同于flex:0 0 auto；
- flex:auto等同于flex:1 1 auto

## 参考: 
- [写给自己看的display: flex布局教程](https://www.zhangxinxu.com/wordpress/2018/10/display-flex-css3-css/)
