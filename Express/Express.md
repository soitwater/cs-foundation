# Express

## 安装
- cnpm install express-generator -g
- cnpm install express -g
- express myapp
- cd myapp
- cnpm install
- npm start 或 node app.js
- 访问 localhost:3000

## 常规书写
```js
一个重要的理解
假如你的nodeJS接口都写在index.js中，该文件的大致内容如下：
let express = require('express')
let app = express()
let bodyParser = require('body-parser')
let urlencodedParser = bodyParser.urlencoded({ extended: false })

app.use(express.static(dataSavePath))

app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept")
  next()
})

app.use(bodyParser.json())

app.get('/csy/getTreeList', urlencodedParser, function (req, res) {

})

app.post('/csy/postTreeList', urlencodedParser, function (req, res) {

})

function unzipFile (userPath) {

}

let server = app.listen(8083, function () {
})

在浏览器每一次发送请求时，在index.js中，除了let 的变量声明，除了function声明
其他的express的app.use  app.get  app.post 等都会被运行一次，它们都被用于解析请求
其中，app.use()就是中间件；app.get  app.post等就是路由

```
## 中间件
-  不是特别的东西，相当于中间函数，一个大的流程被切分成多个小的流程, 它有固定写法：  
   路由其实是特殊的中间件，只有url符合的请求才会进入该路由（中间件）
   ```js
   app.use(function (req, res, next) {
     dosomething()
     next()
   })
   ```
- 关于next()  
  在中间件中 —— express() 的本质是一个对象，中间件是express()对象的一个个属性  
  在解析接受到的请求时，会一个个的执行这些中间件(express.get() 等虽然是路由，但本质上也是特殊的中间件)  
  中间件中，需要标明 next(), 否则中间件无法连续执行。  
  但为什么 `app.use(bodyParser.json())` 也是没有 next() ，它却可以 不中断整条中间件链的执行？  
  若中间件进入了一个router对象，那么原来的中间件链会中断，但程序会进入新的中间件链。  

## 路由
- 含参的通配路由
  含参的通配路由`"/select/:id"`是在 express的`app.post(url, callback)`中才是这么写的，  
  在vue中的axios中还是`"/select/4"`，是没有"冒号"的  
  如`"/csy/apply/select/:id"`，那获取其中的url参数使用 `req.params.id`
