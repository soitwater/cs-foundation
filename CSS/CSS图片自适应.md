# CSS图片自适应  

## 失真
```
img {
  width: 100%;
  height: 100%;  
}
```

## 按比例缩小
```
img {
  max-width: 100%;
  max-height: 100%;  
}
```

## HTML5特性 srcset
```
<img src="./128px.jpg"
  srcset="./128px.jpg 128w, ./256px.jpg 256w, ./512px.jpg 512w"
  sizes="(max-width: 360px) calc(100vw-20px) 128px"/>
```  
## 参考: 
- [CSS图片自适应](https://www.zhangxinxu.com/wordpress/2014/10/responsive-images-srcset-size-w-descriptor/)
