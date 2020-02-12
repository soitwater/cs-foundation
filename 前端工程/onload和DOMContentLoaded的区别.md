# onload和DOMContentLoaded的区别

```js
window.addEventListener('load',function(){
  //页面全部资源加载完才会执行，包括图片、视频等
})

document.addEventListener('DOMContentLoaded',function(){
  //DOM渲染完即可执行，此时图片、视频等可能还未加载完
}
```