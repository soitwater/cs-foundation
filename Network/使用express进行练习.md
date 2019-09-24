# 使用express进行练习

## index.html
```html

```

## app.js
### cookie
- npm i cookie-parser // 假如已经安装则不需要重复安装(因为是官方内置的)
- ```js
  const cookieParase = require('cookie-parser');
  app.use(cookieParase())	// 解析cookie
 
  app.use('/', function (req,res) {
    console.log(req.headers.cookie); // 打印cookie
    res.cookie('user', 'XiaoMing'); // 设置cookie(即给浏览器的响应首部字段中有 Set-Cookie:user=XiaoMing;)
    res.send('succ')
  })
  ```

### 缓存
- 强制缓存HTTP/1.1(Cache-Control)
  ```js
  app.get('/getFile', function (req, res) {			
    var txt = fs.readFileSync(path.resolve(__dirname, 'dist/file.txt'), 'utf-8');			
    res.setHeader("Cache-Control", "public,max-age=600")	// 缓存	
    res.send(txt);											
  });		
  ```
  点击chrome浏览器控制台-Network，此时可以看到"size"显示为(from disk cache)  

- 协商缓存HTTP/1.0(Last-Modified)
  ```js
  app.get('/getFile', function (req, res) {
    let filePath = path.resolve(__dirname, 'dist/file.txt')	 
    fs.stat(filePath, (err, start) => {
      if (req.headers['if-modified-since'] === start.mtime.toUTCString()) {
        res.writeHead(304, 'Not Modified');
        res.end();
      } else {
        fs.readFile(filePath, function (err, data) {
          let lastModified = start.mtime.toUTCString();
          res.setHeader('Last-Modified', lastModified);
          res.writeHead(200, 'OK');
          res.end(data);
        })
      }
    })											
  });		
  ```

## 参考
- [Express使用进阶：cookie-parser中间件实现深入剖析](https://www.cnblogs.com/chyingp/p/express-cookie-parser-deep-in.html)
- [express中cookie的使用和cookie-parser的解读](https://segmentfault.com/a/1190000004139342?_ea=504710)
- [NodeJS文档](http://nodejs.cn/api/http.html#http_response_setheader_name_value)
- [10分钟彻底搞懂Http的强制缓存和协商缓存](https://segmentfault.com/a/1190000016199807)
- [前端缓存最佳实践](https://www.cnblogs.com/xiaoweihuang/p/10134656.html)