# axios

## 特性
- 适用于浏览器 以及 node.js 
- 支持 Promise API
- 拦截请求和响应
- 客户端支持防御 XSRF

## 拦截器
> - 在请求或响应被 then 或 catch 处理前拦截它们（拦截器可以做什么：在请求或者响应时拦截下来进行处理）  
> - 拦截器分为请求拦截器和响应拦截器  
> - 请求拦截器（interceptors.requst）是指可以拦截每次或指定HTTP请求，并可修改配置项。
> - 响应拦截器（interceptors.response）可以在每次HTTP请求后拦截住每次或指定HTTP请求，并可修改返回结果项
- 拦截器的写法看下文的`常用配置`
- 移除拦截器的写法
  ```js
  let myRequestInterceptor = axios.interceptors.request.use(function () {})
  axios.interceptors.request.eject(myRequestInterceptor)
  ```

## 常用配置
```js
import axios from 'axios'
import qs from 'qs'

// axios 配置
axios.defaults.timeout = 100000;
axios.defaults.headers.post['Content-Type'] = 'application/json;charset=UTF-8' // 默认请求头
axios.defaults.baseURL = 'http://localhost:8083/csy' // 后端API Router注意前缀是 /csy

// 请求拦截器
axios.interceptors.request.use((config) => {
  if(config.method === 'post') { // post请求序列化
    config.data = qs.stringify(config.data)
  }
  return config // 返回config
}, (error) => {
  return Promise.reject(error)
})

// 响应拦截器
axios.interceptors.response.use((res) => {
    return res
  }, (error) => {
    return Promise.reject(error)
  }
)

export default {
  // 获取用户文件目录树
  getTreeList: (userId) => axios.get('/tree/getlist/' + userId), 
  // 上传文件
  uploadFile: data => axios.create({headers:{'Content-Type':'multipart/form-data'}}).post('/file/uploadshp', data), 
  // 用户登陆 -- 跳转到 apply 页面
  loginSelect: (data) => axios.create({headers:{"Content-type":"application/x-www-form-urlencoded"}}).post("/login/select", data), 
}
```
前端使用接口
```js
import api from '@/api/index.js'

api.getTreeList(this.userId).then(res => { // this.userId: "1"
  if (res.data !== false) {
		let treeDataArr = JSON.parse(res.data)
	}
}).catch( err => {
	this.$message.error("获取文件目录失败!")
})

let param = new FormData() // 创建form对象
param.append('userId', this.userId)
param.append('file',file) // 通过append向form对象添加数据
api.uploadFile (param).then (res => {
	if (res.data === this.successSign) {
		this.$message.success("上传成功!")
	}
}).catch ( err => {
	console.log (`error --> \n ${err}`)
})
```
后台获取参数
```js
app.use(express.static('js'));

app.use(function(req, res, next) {
res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE');
res.header('Access-Control-Allow-Credentials','true');
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});
var urlencodedParser = bodyParser.urlencoded({ extended: false })

api.get('/getlist/:id', urlencodedParser, function (req, res) {
  let id = req.params.id
})
api.post("/select", urlencodedParser, (req, res) => {
  let password = req.body.password
})
```

## axios为请求添加token
待补充

## 中文
- axios抓取中文(`GBK`)网页内容时,由于默认的编码是`utf8`,因此会出现乱码问题
- 如果是在前端使用`axios`(而不是在`nodejs`端),那么可以`axios`将返回的数据转换为`blob`,再转换为`gbk`编码的`string`
- 如果是在后端使用`axios`(`nodejs`端)
  ```js
  const iconv = require('iconv-lite')
  axios({
    method: 'get',
    url: path,
    responseType: 'arraybuffer'
  }).then(res => {
    let d = res.data
    let d2 = iconv.decode(new Buffer(d), 'GBK')
    console.log('内容??\n', d2)
  })
  ```


## 参考
- [Jquery ajax, Axios, Fetch区别之我见](https://segmentfault.com/a/1190000012836882)
- [中文文档](http://www.axios-js.com/zh-cn/docs/index.html)
- [一个axios的简单教程](https://www.jianshu.com/p/13cf01cdb81f)
- [老司机带路：《axios从入门到开车 嘀嘀~~》](https://www.cnblogs.com/lguow/p/9164171.html)