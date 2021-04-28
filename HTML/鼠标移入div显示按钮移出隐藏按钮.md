# 鼠标移入div显示按钮移出隐藏按钮

## 示例
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <style>
    .father {
      background-color: burlywood;
      height: 300px;

    }

    .father:hover .son {
      /* 设置父元素hover时子元素的样式 */
      display: block;
    }

    .son {
      background-color: cadetblue;
      height: 200px;
      /* 让按钮浮动在父元素上 */
      display: none;
    }
  </style>
</head>

<body>
  <div class="father">
    <button class="son">我是按钮</button>
  </div>
</body>

</html>
```