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
| canvas                                   | svg                                              |
| ---------------------------------------- | ------------------------------------------------ |
| 依赖分辨率（位图）                       | 不依赖分辨率（矢量图）                           |
| 单个 HTML 元素                           | 每一个图形都是一个 DOM 元素                      |
| 只能通过脚本语言绘制图形                 | 可以通过 CSS 也可以通过脚本语言绘制              |
| 不支持事件处理程序                       | 支持事件处理程序                                 |
| 弱的文本渲染能力                         | 最适合带有大型渲染区域的应用程序（比如谷歌地图） |
| 图面较小，对象数量较大（>10k）时性能最佳 | 对象数量较小 (<10k)、图面更大时性能更佳          |

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

## API
- 获取 Canvas 对象
  * `canvas.getContext(contextType, contextAttributes);`
    - contextType 取值
      * 2d（本小册所有的示例都是 2d 的）：代表一个二维渲染上下文
      * webgl（或"experimental-webgl"）：代表一个三维渲染上下文
      * webgl2（或"experimental-webgl2"）：代表一个三维渲染上下文；这种情况下只能在浏览器中实现 WebGL 版本2 (OpenGL ES 3.0)。
- 路径
  * fill() 填充路径
  * stroke() 描边
  * arc() 创建圆弧
  * rect() 创建矩形
  * fillRect() 绘制矩形路径区域
  * strokeRect() 绘制矩形路径描边
  * clearRect() 在给定的矩形内清除指定的像素
  * arcTo() 创建两切线之间的弧/曲线
  * beginPath() 起始一条路径，或重置当前路径
  * moveTo() 把路径移动到画布中的指定点，不创建线条
  * lineTo() 添加一个新点，然后在画布中创建从该点到最后指定点的线条
  * closePath() 创建从当前点回到起始点的路径
  * clip() 从原始画布剪切任意形状和尺寸的区域
  * quadraticCurveTo() 创建二次方贝塞尔曲线
  * bezierCurveTo() 创建三次方贝塞尔曲线
  * isPointInPath() 如果指定的点位于当前路径中，则返回 true，否则返回 false
- 画笔样式(属性)
  * lineCap 设置或返回线条的结束端点样式
  * lineJoin 设置或返回两条线相交时，所创建的拐角类型
  * lineWidth 设置或返回当前的线条宽度
  * miterLimit 设置或返回最大斜接长度
- `fill()`或`stroke()`的样式（属性）
  * fillStyle 设置或返回用于填充绘画的颜色、渐变或模式
  * strokeStyle 设置或返回用于笔触的颜色、渐变或模式
  * shadowColor 设置或返回用于阴影的颜色
  * shadowBlur 设置或返回用于阴影的模糊级别
  * shadowOffsetX 设置或返回阴影距形状的水平距离
  * shadowOffsetY 设置或返回阴影距形状的垂直距离
- 渐变
  * createLinearGradient() 创建线性渐变（用在画布内容上）
  * createPattern() 在指定的方向上重复指定的元素
  * createRadialGradient() 创建放射状/环形的渐变（用在画布内容上）
  * addColorStop() 规定渐变对象中的颜色和停止位置
- 图形转换
  * scale() 缩放当前绘图至更大或更小
  * rotate() 旋转当前绘图
  * translate() 重新映射画布上的 (0,0) 位置
  * transform() 替换绘图的当前转换矩阵
  * setTransform() 将当前转换重置为单位矩阵，然后运行 transform()
- 图像绘制
  * `context.drawImage(img,sx,sy,swidth,sheight,x,y,width,height);`
    - img：规定要使用的图像、画布或视频
    - sx：可选。开始剪切的 x 坐标位置
    - sy：可选。开始剪切的 y 坐标位置
    - swidth：可选。被剪切图像的宽度
    - sheight：可选。被剪切图像的高度
    - x：在画布上放置图像的 x 坐标位置
    - y：在画布上放置图像的 y 坐标位置
    - width：可选。要使用的图像的宽度（伸展或缩小图像）
    - height：可选。要使用的图像的高度（伸展或缩小图像）

## Canvas绘制例子移动动画
- `context.clearRect(x,y,width,height);` （清除画板）
    * x：要清除的矩形左上角的 x 坐标
    * y：要清除的矩形左上角的 y 坐标
    * width：要清除的矩形的宽度，以像素计
    * height：要清除的矩形的高度，以像素计
- 绘制用方法`requestAnimationFrame()`
- 代码
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <style>
          html, body {
            margin: 0;
            overflow: hidden;
            width: 100%;
            height: 100%;
            cursor: none;
            background: black;
          }
      </style>
  </head>
  <body>
  <canvas id="canvas"></canvas>

  <script>
      var ctx = document.getElementById('canvas'),
        content = ctx.getContext('2d'),
        round = [],
        WIDTH,
        HEIGHT,
        initRoundPopulation = 80;


      WIDTH = document.documentElement.clientWidth;
      HEIGHT = document.documentElement.clientHeight;

      ctx.width = WIDTH;
      ctx.height = HEIGHT;

      function Round_item(index, x, y) {
        this.index = index;
        this.x = x;
        this.y = y;
        this.r = Math.random() * 2 + 1;
        var alpha = (Math.floor(Math.random() * 10) + 1) / 10 / 2;
        this.color = "rgba(255,255,255," + alpha + ")";
      }

      Round_item.prototype.draw = function () {
        content.fillStyle = this.color;
        content.shadowBlur = this.r * 2;
        content.beginPath();
        content.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false);
        content.closePath();
        content.fill();
      };

      function animate() {
        content.clearRect(0, 0, WIDTH, HEIGHT);

        for (var i in round) {
          round[i].move();
        }
        requestAnimationFrame(animate)
      }

      Round_item.prototype.move = function () {
        this.y -= 0.15;
        if (this.y <= -10) {
          this.y = HEIGHT + 10;
        }
        this.draw();
    };


      function init() {
        for (var i = 0; i < initRoundPopulation; i++) {
          round[i] = new Round_item(i, Math.random() * WIDTH, Math.random() * HEIGHT);
          round[i].draw();
        }
        animate();
      }

      init();
  </script>
  </body>
  </html>
  ```

## Canvas绘制鼠标拖影动画
- 代码
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <style>
          * {
              padding: 0;
              margin: 0;
          }

          #canvas {
              background: #000;
          }
      </style>
  </head>
  <body>
  <canvas id="canvas"></canvas>
  <script>
    var canvas = document.getElementById('canvas'),
      ctx = canvas.getContext('2d'),
      WIDTH = canvas.width = document.documentElement.clientWidth,
      HEIGHT = canvas.height = document.documentElement.clientHeight,
      para = {
        num: 100,
        color: false,    //  颜色  如果是false 则是随机渐变颜色
        r: 0.9,
        o: 0.09,         //  判断圆消失的条件，数值越大，消失的越快
        a: 1,
      },
      color,
      color2,
      round_arr = [];

    window.onmousemove = function (event) {
      mouseX = event.clientX;
      mouseY = event.clientY;
      round_arr.push({
        mouseX: mouseX,
        mouseY: mouseY,
        r: para.r,
        o: 1
      })
    };

    // 判断参数中是否设置了 color，如果设置了 color，就使用该值、
    // 如果参数中的 color 为 false，那么就使用随机的颜色
    if (para.color) {
      color2 = para.color;
    } else {
      color = Math.random() * 360;
    }

    function animate() {
      if (!para.color) {
        color += .1;
        color2 = 'hsl(' + color + ',100%,80%)';
      }

      ctx.clearRect(0, 0, WIDTH, HEIGHT);

      for (var i = 0; i < round_arr.length; i++) {
        ctx.fillStyle = color2;
        ctx.beginPath();
        ctx.arc( round_arr[i].mouseX ,round_arr[i].mouseY,round_arr[i].r,0, Math.PI * 2);
        ctx.closePath();
        ctx.fill();
        round_arr[i].r += para.r;
        round_arr[i].o -= para.o;

        if( round_arr[i].o <= 0){
          round_arr.splice(i,1);
          i--;
        }
      }
      window.requestAnimationFrame(animate);
    };

    animate();
  </script>
  </body>
  </html>
  ```

## 优化
- 避免浮点数的坐标点（原因在于 Canvas 支持半个像素绘制，会根据小数位实现插值算法实现绘制图像的反锯齿效果）
- 使用多层画布去画一个复杂的场景（游戏中经常移动的元素和不经常移动的元素分层，避免不必要的重绘）
- 用 CSS `transform` 特性缩放画布（用 `left`、`top` 这些属性写动画会回流）
- 离屏渲染（缓存，屏幕之外的地方绘制好，建立两个`Canvas`标签，大小一致，一个正常显示，一个隐藏（缓存用的，不插入`DOM`中）

## 随机例子特效
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <style>
    html,
    body {
      margin: 0;
      overflow: hidden;
      width: 100%;
      height: 100%;
      cursor: none;
      background: black;
    }
  </style>
</head>

<body>
  <canvas id="canvas"></canvas>

  <script>
    // 创建全屏画布
    var ctx = document.getElementById('canvas'),
      content = ctx.getContext('2d'),
      WIDTH,
      HEIGHT,
      round = [],
      initRoundPopulation = 80;

    WIDTH = document.documentElement.clientWidth;
    HEIGHT = document.documentElement.clientHeight;

    ctx.width = WIDTH;
    ctx.height = HEIGHT;

    // 圆类，可创建随机的圆，并使用 index 标识  
    function Round_item(index, x, y) {
      this.index = index;
      this.x = x;
      this.y = y;
      this.r = Math.random() * 2 + 1;
      var alpha = (Math.floor(Math.random() * 10) + 1) / 10 / 2;
      this.color = "rgba(255,255,255," + alpha + ")";
    }
    Round_item.prototype.name = 'xxx'
    // 绘制方法
    Round_item.prototype.draw = function () {
      content.fillStyle = this.color;
      content.shadowBlur = this.r * 2;
      content.beginPath();
      content.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false);
      content.closePath();
      content.fill();
    };
    // 初始化若干个 圆
    function init() {
      for (var i = 0; i < initRoundPopulation; i++) {
        round[i] = new Round_item(i, Math.random() * WIDTH, Math.random() * HEIGHT);
        console.log(round[i].name)
        console.log(round[i])
        round[i].draw();
      }
    }

    init();
  </script>
</body>
</html>
```

