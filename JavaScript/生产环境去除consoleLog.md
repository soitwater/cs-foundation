# 生产环境去除console.log

## 代码
```js
if(process.env.NODE_ENV === 'production') {
  console.log = function () {};
}
```