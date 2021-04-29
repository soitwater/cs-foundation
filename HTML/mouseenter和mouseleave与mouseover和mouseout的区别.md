# mouseenter和mouseleave与mouseover和mouseout的区别

## mouseenter（指针穿过，子集不执行）
- 当鼠标指针穿过（进入）被选元素时，会发生 mouseenter 事件。
- 注意：与 mouseover 事件不同，mouseenter 事件只有在鼠标指针进入被选元素时被触发，mouseover 事件在鼠标指针进入任意子元素时也会被触发。

## mouseover（指针上方，子元素执行）
- 当鼠标指针位于元素上方时，会发生 mouseover 事件。
- 注意：与 mouseenter 事件不同，mouseover 事件在鼠标指针进入被选元素或任意子元素时都会被触发，mouseenter 事件只有在鼠标指针进入被选元素时被触发。
- 先执行mouseover后mouseenter
- mouseover/mouseout事件支持冒泡，绑定的事件会冒泡到子标签上。
- mouseenter/mouseleave事件则只针对所绑定的标签，不会冒泡到子标签上。 
- mouseover可以看到进入父集元素，进入到子集时、离开子集进入父级，一共触发三次，而mouseout只有进父级时一次

## mouseleave（指针离开元素时，子元素不执行）
- 当鼠标指针离开元素时，会发生 mouseleave 事件。
- 该事件大多数时候会与 mouseenter 事件一起使用。

## mouseout(指针从元素上移开,子元素执行)
- 当鼠标指针从元素上移开时，发生 mouseout 事件。
- 该事件大多数时候会与 mouseover 事件一起使用。
- 注释：与 mouseleave 事件不同，不论鼠标指针离开被选元素还是任何子元素，都会触发 mouseout 事件。只有在鼠标指针离开被选元素时，才会触发 mouseleave 事件。
- mouseout可以看到离开父集进入子集，出子集元素，出父级元素一共三次，而mouseout只有出父级时一次

## 示例
```html
<!DOCTYPE html>
<html>

<head>
</head>

<body>
  <div class="box">
    <div class="sonbox"></div>
  </div>
</body>
<script type="text/javascript">
  var box = document.getElementsByClassName("box")[0];
  box.addEventListener("mouseenter", function () {
    console.log("MouseEnter!");
  });
  box.addEventListener("mouseover", function () {
    console.log("MouseOver!");
  });
  box.addEventListener("mouseout", function () {
    console.log("MouseOut!");
  });
  box.addEventListener("mouseleave", function () {
    console.log("MouseLeave!");
  });
</script>
<style>
  div {
    margin: 0 auto;
  }

  .box {
    box-sizing: border-box;
    padding: 30px 0;
    width: 300px;
    border: 1px solid red;
  }

  .sonbox {
    width: 150px;
    height: 150px;
    background-color: pink;
  }
</style>

</html>
```