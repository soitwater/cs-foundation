# Promise实现请求超时

抓住 promise 的状态只能由 pending -> fulfilled，或者 pending -> rejected，并且只能进行一次改变  
后端
```js
app.get("/asyncrequest", function (req, res) {
  let delay = 4000
  setTimeout(() => {
    res.end("delay to send message: " + delay)
  }, delay)
})
```
前端
```js
function promiseTimeout (url, timeout = 3000) {
  return new Promise ((resolve, reject) => {
    fetch(url).then(data => resolve(data.text())) // 无超时则输出响应信息
    setTimeout(() => reject(Error("exec timeout")), timeout) // 超时则打印错误：exec timeout
  })
}
promiseTimeout("http://localhost:8081/asyncrequest")
  .then((data) => console.log(data))
  .catch((err) => console.log(err))
```


## 参考
- [面经](https://github.com/lawler61/blog/issues/7)