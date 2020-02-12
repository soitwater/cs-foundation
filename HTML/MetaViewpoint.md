# MetaViewpoint
## 概念
### ideal viewpoint 写法
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```
### Meta viewpoint 属性  
| a             | b                                                                 |
| ------------- | ----------------------------------------------------------------- |
| width         | 设置layout viewport  的宽度，为一个正整数，或字符串"width-device" |
| initial-scale | 设置页面的初始缩放值，为一个数字，可以带小数                      |
| minimum-scale | 允许用户的最小缩放值，为一个数字，可以带小数                      |
| maximum-scale | 允许用户的最大缩放值，为一个数字，可以带小数                      |
| height        | 设置layout viewport  的高度，这个属性对我们并不重要，很少使用     |
| user-scalable | 是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许 |

## 参考
- [移动前端开发之viewport的深入理解](https://www.cnblogs.com/2050/p/3877280.html)