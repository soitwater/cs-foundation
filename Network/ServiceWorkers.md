# ServiceWorkers
丰富的离线体验，定期后台同步，推送通知 （一般这是原生app做的事）, Service Workers提供了这些功能的技术基础

## 功能
- service worker是一个脚本，由浏览器在背后默默地运行，独立于web页面。
- 包含功能(不需要页面辅助/用户操作 的功能)
  * 推送通知
  * 离线缓存(细粒度地缓存资源,且可以完全控制在特定情形下的表现,如离线)
  * 后台同步 / 静默更新
  * 定期同步
  * 地理防护
- 特点
  * 网站必须使用 HTTPS。除了使用本地开发环境调试时(如域名使用 localhost)(因为Service Workers有操作网络请求的能力,应避免暴露给中间人)(Github Pages 也是HTTPS)
  * 运行于浏览器后台，可以控制打开的作用域范围下所有的页面请求
  * 单独的作用域范围，单独的运行环境和执行线程（无法操作DOM,但可由页面主动`postMessage`,`ServiceWorkers`监听并间接操作DOM）
  * 不能操作页面 DOM。但可以通过事件机制来处理（ServiceWorkers 是 WebWorkers 的子集）

## 使用
### 注册 Service Workers
express服务(输入网址`http://localhost:8080/a3`)
```js
var app = express();	
app.use(cookieParase())	// 解析cookie									
app.use(bodyParser.json());											
app.use(bodyParser.urlencoded({ extended: false }));		
// 跨域
app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept")
  next()
})
// 访问静态资源											
app.use(express.static(path.resolve(__dirname, './dist')));				
// 访问单页											
app.get('/a3', function (req, res) {											
  var html = fs.readFileSync(path.resolve(__dirname, 'dist/index.html'), 'utf-8');	
  res.send(html);											
})
// 监听											
app.listen(8080, function () {											
  // console.log('success listen...');											
})
```
在页面上注册`Service Workers`(检查service worker服务是否可用，在一旦页面load完成完成后，注册/sw.js)(文件夹:`根路径/dist/...`)  
dist/serviceWorkers.js  
```js
this.addEventListener('install', function (event) {
  event.waitUntil(
    caches.open('v1').then(function (cache) {
      return cache.addAll([
        './index3.html' // 需要有这个文件
      ]);
    })
  );
});
```
dist/index.html
```html
<!doctype html>
<html>
  <head>
    <title>service</title>
  </head>
  <body>
    <div>index2.html</div>
    <script type="text/javascript">
      if ('serviceWorker' in navigator) {
        window.addEventListener('load', function () {
          console.log(navigator.serviceWorker)
          navigator.serviceWorker.register('./serviceWorkers.js') // 需要有这个文件
            .then(function (registration) {
              console.log('ServiceWorker registration successful with scope: ', registration.scope)
            })
        });
      }
    </script>
  </body>  
</html>
```
在浏览器输入url`chrome://inspect/#service-workers`可以检查service workers

## 问题
### 为什么不使用`Web Workers`
Web Worker 是临时的，每次做的事情的结果还不能被持久存下来，如果下次有同样的复杂操作，还得费时间的重新来一遍。


## 参考
- [Service Workers简介(一)](https://www.jianshu.com/p/1bc5bf8be43d)
- [使用Service Worker和离线缓存Cache](https://www.jianshu.com/p/778f37db5a49)
- [网站渐进式增强体验(PWA)改造：Service Worker 应用详解](https://lzw.me/a/pwa-service-worker.html)