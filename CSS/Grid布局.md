# Grid布局

## Grid属性大全
- 网格容器(Grid Container) 属性
  ```css
  display
  grid-template-columns
  grid-template-rows
  grid-template-areas
  grid-template
  grid-column-gap
  grid-row-gap
  grid-gap
  justify-items
  align-items
  place-items
  justify-content
  align-content
  place-content
  grid-auto-columns
  grid-auto-rows
  grid-auto-flow
  grid
  ```
- 网格项(Grid Items) 属性
  ```css
  grid-column-start
  grid-column-end
  grid-row-start
  grid-row-end
  grid-column
  grid-row
  grid-area
  justify-self
  align-self
  place-self
  ```

## 示例
```css
display: grid
grid-template-columns: repeat(12, 1fr);  // 12列，每列占  1/12

grid-gap：5px； // 网格项之间的间隙是5px				

/* 表示为3*3的格网布局命名 */		
grid-template-areas：“h h h”				
                     "m m c"				
                     "c f f"        				
```				
```css				
.header {					
    grid-area: h;					
}					
.menu {					
    grid-area: m;					
}					
.content {					
    grid-area: c;					
}					
.footer {					
   grid-area: f;					
}					
// grid-area为div与grid中的网格项建立联系					  
// 通过 修改 grid-template-areas ，可以快速修改布局					
// 可以使用点 . 来创建空白的网格单元格（相当于占位符）					
					
					
grid-template-rows 1fr 9fr					
这里的布局不是1：9，而是1：1，为什么？					
后来发现是因为类名重复导致样式效果叠加，简而言之就是 style的scope失效					
因为你写错了，是scoped而不是scope					
另外还是尽量类名不同吧					

```
## 参考
- [如何使用 CSS Grid 快速而又灵活的布局](https://www.html.cn/archives/8512)