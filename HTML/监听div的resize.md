# 监听div的resize

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <style>
    #test-re {
      position: absolute;
      width: 100px;
      height: 100px;
      /* 使得div可resize */
      resize: both;
      overflow: auto;
    }

    #main-wrapper {
      /* 令宽高等于父元素div的100%,当div宽高变化则触发iframe的resize事件 */
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
      z-index: -1;
      background: yellow;
    }
  </style>
</head>

<body>
  <div id="test-re">
    <span>123</span>
    <iframe id="main-wrapper" name="main-wrapper" src="" frameborder="0"></iframe>
  </div>
  <script>
    // 第一种方式
    let iframeEl = document.querySelector('#main-wrapper');
    iframeEl.contentWindow.addEventListener('resize', () => {
      console.log('iframe resize');
    });
    // 第二种方式
    let MutationObserver = window.MutationObserver || window.WebKitMutationObserver || window.MozMutationObserver;
    let element = document.querySelector('#test-re');
    let observer = new MutationObserver((mutationList) => {
      let width = getComputedStyle(element).getPropertyValue('width')
      let height = getComputedStyle(element).getPropertyValue('height')
      console.log('div的宽高发生改变:', mutationList, width, height)
    });
    observer.observe(element, { attributes: true, attributeFilter: ['style'], attributeOldValue: false });
  </script>
</body>

</html>
```