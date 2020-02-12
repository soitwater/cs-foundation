# requestAnimationFrame

- 一般JS进行动画渲染使用setInterval / setTimeout, 但是这两者的时间间隔并不准确,因此容易造成动画卡顿。requestAnimationFrame可以解决这个问题
- requestAnimationFrame 本质是一个节流的setTimeout
- requestAnimationFrame是交由系统进行动画渲染,动画的刷新频率跟随系统设置
- requestAnimationFrame只关心动画每一帧的改变
- 使用
  ```js
  var progress = 0;
  //回调函数
  function render() {
    progress += 1; //修改图像的位置
    if (progress < 100) {
      //在动画没有结束前，递归渲染
      window.requestAnimationFrame(render);
    }
  }
  //第一帧渲染
  window.requestAnimationFrame(render);
  ```

## 参考
- [requestAnimationFrame 知多少？](https://www.cnblogs.com/onepixel/p/7078617.html)