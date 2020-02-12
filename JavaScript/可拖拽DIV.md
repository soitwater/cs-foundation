# 可拖拽DIV

```html
<!DOCTYPE html>
  <head>
    <title>overflow</title>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
	<style>
	  div {
	    position: fixed;
	    background: rgba(255, 0, 0, .5);
		width: 200px;
		height: 200px;
	  }
	</style>
  </head>
  <body>
    <div></div>
	<script>
	  let el = document.getElementsByTagName("div")[0]
	  el.onmousedown = function (e) {
      let disx = e.pageX - el.offsetLeft
      let disy = e.pageY - el.offsetTop
      document.onmousemove = function (e) {
        el.style.left = e.pageX - disx + 'px'
        el.style.top = e.pageY - disy + 'px'
      }
      document.onmouseup = function (e) {
        document.onmouseup = document.onmousemove = null
      }
      e.preventDefault()
    }
	  
	  el.ontouchstart = function (e) {
      e.preventDefault()
      let touch = e.touches[0] || e.originEvent.touches[0] || e.originEvent.changedTouches[0]
      let disx = touch.pageX - el.offsetLeft
      let disy = touch.pageY - el.offsetTop
      el.ontouchmove = function (e) {
        let touch = e.touches[0] || e.originEvent.touches[0] || e.originEvent.changedTouches[0]
        el.style.left =  touch.pageX - disx + "px"
        el.style.top = touch.pageY - disy + "px"
      }
      el.ontouchend = function (e) {
        el.ontouchmove = el.ontouchend = null
      }
    }
	</script>
  </body>
</html>
```