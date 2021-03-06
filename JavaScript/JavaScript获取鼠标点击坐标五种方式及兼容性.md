# JavaScript 获取鼠标点击坐标五种方式及兼容性

## 类型
- event.clientX/Y
- event.pageX/Y
- event.offsetX/Y
- event.layerX/Y
- event.screenX/Y

## 具体
- clientX/Y:  
  > clientX/Y获取到的是触发点相对浏览器可视区域左上角距离，不随页面滚动而改变  
  > 兼容性：所有浏览器均支持

- pageX/Y:
  > pageX/Y获取到的是触发点相对文档区域左上角距离，会随着页面滚动而改变
  > 兼容性：除IE6/7/8不支持外，其余浏览器均支持

- offsetX/Y:
  > offsetX/Y获取到是触发点相对被触发dom的左上角距离，不过左上角基准点在不同浏览器中有区别，  
  > 其中在IE中以内容区左上角为基准点不包括边框，如果触发点在边框上会返回负值，而chrome中以边框左上角为基准点。  
  > 兼容性：IE所有版本，chrome，Safari均完美支持，Firefox不支持

- layerX/Y:
  > layerX/Y获取到的是触发点相对被触发dom左上角的距离，数值与offsetX/Y相同，这个变量就是firefox用来替代offsetX/Y的，
  > 基准点为边框左上角，
  > 但是有个条件就是，被触发的dom需要设置为position:relative或者position:absolute，否则会返回相对html文档区域左上角的距离
  > 兼容性：IE6/7/8不支持，opera不支持，IE9/10和Chrome、Safari均支持

- screenX/Y:
  > screenX/Y获取到的是触发点相对显示器屏幕左上角的距离，不随页面滚动而改变
  > 兼容性：所有浏览器均支持

## 参考
- [JavaScript 获取鼠标点击坐标五种方式及兼容性](https://blog.csdn.net/chy555chy/article/details/54584985)