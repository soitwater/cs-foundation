# Express上传文件

## 前端部分的代码
```html
<!-- 标签input需要name属性,否则不能POST? -->
<!-- 事件必须是 change，若为click，那么在点击时就已经post了，且change要求这次上传的文件与上次不同 -->
<input id="uploadFile" type="file" @change="uploadFileFunction(node, data)" ref="uploadFileBtn" name="uploadFile" multiple='multiple'/>
```
```js
let input = this.$refs.uploadFileBtn			
// 点击了“取消按钮” -- 文件为空时，不发送post请求			
if (input.files.length === 0) {			
  return			
}			
let file = input.files[0]			
let param = new FormData() // 创建form对象			
param.append('userId', this.userId)			
param.append('fileId', d.id)			
param.append('file',file) // 通过append向form对象添加数据			
// 注意files.length 获取文件长度			
api.uploadFile (param).then (res => {			
  if (res.data === this.successSign) {			
    this.$message.success("上传成功!")			
  }			
  return 			
}).catch ( err => {			
  console.log (`error --> \n ${err}`)			
})			
// 注意这里的api.uploadFile : 			
uploadFile: data => axios.create({headers:{'Content-Type':'multipart/form-data'}}).post('/uploadFile', data), // 上传文件			
```

## 常见Content-Type类型有四种：
- application/x-www-form-urlencoded 常见的form提交
- multipart/form-data 文件提交
- application/json 提交json格式的数据
- text/xml 提交xml格式的数据
  
## 模块对比
- body-parser中间件  
这个中间件用于处理第1,3种content-type 的body非常的方便，但不能用于处理multipart类型的body，具体api：
  * bodyParser.json()  将body解析为json
  * bodyParser.text()   将body解析为文本
  * bodyParser.raw()  将body解析为二进制
  * bodyParser.urlencoded()  将编码为URLEncoder的body解析出来

- multer 中间件
只处理类型是multipart/form-data的body。

- Formidable 中间件
在express中可用express-formidable ，可用于处理表单和上传文件，功能大而全,支持4种content-type类型，可配置项非常多，最大的区别是还提供了一个对象,用于处理各种事件
  ```js
  app.post('/uploadFile', urlencodedParser, function (req, res) {										
  // 表单 										
  var form = new formidable.IncomingForm()										
  let targetPath = path.join(dataSavePath) // 文件的保存路径										
  form.uploadDir = targetPath										
  // 下面的方法是异步的？且作用是将获取表单数据--将文件保存到本地										
  form.parse(req, function(err, fields, files) {										
    if (err) {										
      console.log ("获取用户名参数失败 --> \n",err)									
      throw Error("获取用户名参数失败")									
    }									
										
    let oldName = oldName = files.file.path // 临时的文件的 绝对路径 + 文件名 （无扩展后缀）									
    let newName = fields.fileId // 新的文件名（无扩展后缀）									
    let oldpath = path.join(oldName)									
    let extname = path.extname(files.file.name);									
    let newpath = path.join(targetPath, "/" + newName + extname)										
    // 改名									
    fs.rename(oldpath, newpath, function(err) {										
      if(err) {										
        console.log ('改名错误\n  ', err)									
        throw Error("改名失败")									
      }										
      res.send(successSign)									
    })										
  })										
})										
```

## 上传模块
待补充（结合个人项目）

## 上传进度模块


## 注意
只有设置了 name 属性的表单元素才能在提交表单时传递它们的值。
```html
<input type="file" name="upload" multiple="multiple">
```

## 参考
- [Express中间件,bodyparser,multer,formidable区别浅谈](https://www.jianshu.com/p/828fdf02de06)
- [Node.js的Formidable模块的使用](https://www.cnblogs.com/yuanke/archive/2016/02/26/5221853.html)
- [formidable处理提交的表单或图片文件的简单介绍](https://segmentfault.com/a/1190000011424511)
- [node.js接收上传的文件](https://blog.csdn.net/qq_36529459/article/details/80159222)
- [axios用post提交的数据格式](https://blog.csdn.net/wopelo/article/details/78783442)