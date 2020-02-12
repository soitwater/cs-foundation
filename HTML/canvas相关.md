# canvas相关
- `<canvas>`标签自身不带外观,它仅提供一个画板,还需要js的相关api才能显示图形
- webgl是3D版本的canvas
  
## 面试题  
- 怎么在网页上画一个圆？
  * 直接使用图片(不方便修改,图片增加网络负担)
  * 使用 div + CSS3 的 border + border-radius 模拟一个圆。
  * 使用 svg。本质是一个dom元素，由xml控制
  * Canvas + JavaScript 动态画一个圆。


## canvas 以及 svg 之间的区别
canvas|svg
-|-
依赖分辨率（位图）|不依赖分辨率（矢量图）
单个 HTML 元素|每一个图形都是一个 DOM 元素
只能通过脚本语言绘制图形|可以通过 CSS 也可以通过脚本语言绘制
不支持事件处理程序|支持事件处理程序
弱的文本渲染能力|最适合带有大型渲染区域的应用程序（比如谷歌地图）
图面较小，对象数量较大（>10k）时性能最佳|对象数量较小 (<10k)、图面更大时性能更佳

## 注意
- 每个`<canvas>`只能有一个上下文对象`canvas.getContext('2d')`,即使多次调用`canvas.getContext('2d')`也只是返回同一个


## 例子
```html
<!DOCTYPE html>
<head><title>Canvas</title><meta charset="utf-8" /></head>
<body><canvas id="canvas" style='width:1000px;height:1000px;'></canvas>
  <script>
    let canvas = document.getElementById('canvas')
    let context = canvas.getContext('2d') // 执行上下文
    context.fillStyle = '#f00' // 填充颜色为红色
    context.fillRect(0, 0, 50, 50) // 填充一个正方形

    // 线段 + 多边形
    context.beginPath() // 开始一条新的路径(绘制路径并不会渲染线段,还需要调用其他的填充方法)
    context.moveTo(100, 100) // 从(100, 100)开始定义一条新的子路径
    context.lineTo(200, 200) // 从(100, 100)到(200, 200)绘制一条线段
    context.lineTo(100, 200) // 从(200, 200)到(100, 200)绘制一条线段
    context.closePath() // (以光滑方式)闭合路径
    context.fill() // 填充一个三角形区域
    context.stroke() // 绘制三角形的两条边
  </script>
</body>
</html>
```

## 应用场景
- 图表：canvas 使用js动态传入参数进行绘制
- 游戏：几乎所有的HTML5游戏都基于Canvas
- 活动页：例如抽奖页的转盘,网页背景的可交互例子特效...