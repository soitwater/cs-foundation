# HTML邮件模板

## 注意
1. 所有样式必须行内，否则不起作用(写在`标签`上)
2. 所有布局必须使用`table`，复杂布局使用`table嵌套`或单元格合并
3. 有的邮件客户端table默认有边框，需要在代码中取消
4. 元素样式尽量拆开写，不要写`margin:0;`,而是写`margin-top:0;`。同理,`border`，`margin`，`padding`，`background`等等
5. 元素需要取消默认样式,例如`p`标签,`tr`，`tb`标签很多都会有默认的`padding`和`margin`样式,应该写`padding-top:0`
6. 如果有上下间隔，尽量不使用`padding`和`margin`，应使用空的`table`或`tr`元素
7. 没有字号标签都添加`font-size:0px;`, 具体到标签再设置具体字号, 因为有的邮件客户端会塞入大量实体空格标签造成页面布局错乱
8. 邮件模板中最好不使用CSS3，也不是说不能使用，比如`border-radius`在大部分邮箱中是有效的
9. 同一`table`下每个`tr`中`td`的数量要保持一致, 否则有的邮件客户端会补全

## 具体
- `img标签`的宽不用写到style里，写到外面且不需要单位，否则在outlook下会有问题
  ```html
  <img width="60" height="60" alt="" />
  ```