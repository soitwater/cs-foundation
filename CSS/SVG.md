# SVG

## 位置
- 直接放在`<html>`文件中
  ```html
  <svg xmlns="http://www.w3.org/2000/svg" version="1.1" height="190">
  <polygon points="100,10 40,180 190,60 10,60 160,180"
    style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;" />
  </svg>
  <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="800" height="1000">
  <polygon points="800,0 800,1000 600,1000 0,160 200,0"
  style="fill:#eff6fc;stroke-width:0;fill-rule:evenodd;fill-opacity:0.1;" />
  </svg>
  ```
- 放在`.svg`文件中, 并在css中引入
  ```html
  <?xml version="1.0" standalone="no"?>
  <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" 
  "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
  <svg xmlns="http://www.w3.org/2000/svg" version="1.1">
    <circle cx="100" cy="50" r="40" stroke="black"
    stroke-width="2" fill="red" />
  </svg>
  ```

## SVG不显示问题
注意svg文件的排榜整齐, 尤其第一行行首不能有空格.

## 参考
- [SVG 图像入门教程](http://www.ruanyifeng.com/blog/2018/08/svg.html)
- [在线编辑工具](https://c.runoob.com/more/svgeditor/)
- [SVG入门指南](https://github.com/qq449245884/xiaozhi/issues/163)