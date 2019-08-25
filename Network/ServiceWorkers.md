# ServiceWorkers
丰富的离线体验，定期后台同步，推送通知 （一般这是原生app做的事）, Service Workers提供了这些功能的技术基础

## 功能
- service worker是一个脚本，由浏览器在背后默默地运行，独立于web页面。
- 包含功能(不需要页面辅助/用户操作 的功能)
  * 推送通知
  * 离线缓存
  * 后台同步 / 静默更新
  * 定期同步
  * 地理防护
- 特点
  * 网站必须使用 HTTPS。除了使用本地开发环境调试时(如域名使用 localhost)(Github Pages 也是HTTPS)
  * 运行于浏览器后台，可以控制打开的作用域范围下所有的页面请求
  * 单独的作用域范围，单独的运行环境和执行线程（无法操作DOM,但可由页面主动`postMessage`,`ServiceWorkers`监听并间接操作DOM）
  * 不能操作页面 DOM。但可以通过事件机制来处理（ServiceWorkers 是 WebWorkers 的子集）

## 使用
### 注册 Service Workers
在页面上注册`Service Workers`(检查service worker服务是否可用，在一旦页面load完成完成后，注册/sw.js)
```js
if ('serviceWorker' in navigator) {
  window.addEventListener('load', function() {
    navigator.serviceWorker.register('/sw.js').then(function(registration) {
      // Registration was successful
      console.log('ServiceWorker registration successful with scope: ', registration.scope);
    }, function(err) {
      // registration failed :(
      console.log('ServiceWorker registration failed: ', err);
    });
  });
}
```

## 参考
- [Service Workers简介(一)](https://www.jianshu.com/p/1bc5bf8be43d)