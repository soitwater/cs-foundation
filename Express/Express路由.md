# Express路由

## 访问静态资源
```js
app.use('/static', express.static(__dirname + '/public'));
```
访问方式
```
http://localhost:3000/static/hello.html
http://localhost:3000/static/images/1.jpg
http://localhost:3000/static/css/style.css
http://localhost:3000/static/js/index.js
```


## 多级路由
```js
使用Router管理路由
使用方法：
var apiRouter = require("./routes/api_router") // 引入API Router
app.get("/api", apiRouter) // API 文件的调用, 注意这里的get与api router里的get是对应的
在API Router文件中，还需要重新引入 var express = require("express") 等Module的
var api  = express.Router()
api.get("/users", function(req, res) { /* ... */ })
module.exports = api
```

## 前后端交互
```js
axios.create({headers:{"Content-type":"application/x-www-form-urlencoded"}}).post("/apply/:" + id, data)
这种请求头，POST发送的数据是Form Data，可以直接在后台node里req.body获取POST发送的数据
// 前端
import qs from "qs"
qs.stringify(jsonData)
// 后台
req.body.id
而axios.create({headers:{'Content-Type':'multipart/form-data'}}).post('/uploadFile', data)
这种请求头中发送的数据是Request Payload形式，
```
```js
app.use（）
假设express() 为app，
客户端发送的POST请求是 http://localhost:8083/csy/login/isCountExist
那么，实际的path是“/csy/login/isCountExist”
app.use("/csy", require("./router.js"))中, "/csy"是对的，若写成"/csy/login"那么就是错误的, 最多就一个“斜杆”，除非使用正则表达式 
require("./router.js") 表示设置router对象
现在的router.method()，如：router.post()   router.get() 等，在express 4.x版本已经支持在callbock里设置router对象了
在./router.js里，注意：
因为已经写了app.use("/csy", require("./router.js"))
那么./router.js中假如监听的是 http://localhost:8083/csy/login/isCountExist，那就应该写成 router.post('/login/isCountExist', urlencodedParser, function (req, res) {})
注意：没有“/csy”
这里的应用主要有多级路由
```
```js
后台返回是 res.send(something) // data 是字符串, 数组, 或Object
前端用 res.data 接受参数
```
```js
若let express = require("express")
那么 express() 与 express().Router() 的区别是什么?
功能上应该是没有区别的，仅仅是后者用于分级路由管理
```
对于文件
```js
formidable 是遍历 所上传的 new FormData().append("key", someData)的 ，所以不要把需要提前知道的参数(如 用户ID 放入 new FormData().append("key", someData))
可以尝试以下形式：
api.post('/uploadFile/:userId/:fileName', urlencodedParser, function (req, res) {
  let userId = req.params.userId
  let fileName = req.params.fileName
})
let param = new FormData() // 创建form对象
param.append('userId', store.state.userId)
```