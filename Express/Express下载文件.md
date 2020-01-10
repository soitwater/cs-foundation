# Express下载文件

## 前端
前端的请求方式：  
- `<a href="/user/test/xxxx.txt" download="文件名.txt">点击下载</a>`  
  注意download属性
- `window.open(url);`
  url为后端文件下载接口
- 表单提交
  myForm.submit()

## 后端
返回文件到前端，通知浏览器下载文件
`res.download()` 看项目，待补充
(看项目，待补充)

## 要求浏览器直接下载而不是打开
```js
 Invalid character in header content ["Content-Disposition"]
在前端要求下载文件时，express把文件传输过去，因为有的文件会直接被浏览器识别，直接打开，如：json，txt ，而不是下载保存，所以需要设置响应头在express端
res.set({
  'Content-Type': 'application/octet-stream', //告诉浏览器这是一个二进制文件
  'Content-Disposition': 'attachment; filename=a.json'  //告诉浏览器这是一个需要下载的文件
  // 'Content-Length': stats.size //文件大小
})
千万不要写成  'Content-Disposition': 'attachment; filename=' + fileName  否则报错： Invalid character in header content ["Content-Disposition"]
但是在 res.download(jsonFilePath) 源码中 其实 响应头已经设置好了
真正原因是ajax只能获取string信息，无法下载文件，需要：
```

## 参考
- [nodejs实现文件下载功能，防止文本TXT类文件直接打开](https://blog.csdn.net/u013992330/article/details/78486909)