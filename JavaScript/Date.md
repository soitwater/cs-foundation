# Date
关于JS的Date的API

## API
- 获取
  ```javascript
  Date.now ()  // 返回从 1970 年 1 月 1 日至今的毫秒数
  var dt = new Date ()
  dt.getTime () // 获取毫秒数
  dt.getFullYear () // 年
  dt.getMonth () // 月(0-11)  注意 加1
  dt.getDate () // 日 (0-31)
  dt.getHours () // 小时 (0-23)
  dt.getMinutes () // 分钟 (0-59)
  dt.getSeconds () // 秒 (0-59)
  ```

## 获取 2019-05-22 格式的日期
```js
function formatDate (dt) {
  if (!dt) {
    dt = new Date ()
  }
  var year = dt.getFullYear ()
  var month = dt.getMonth () + 1
  var day = dt.getDate ()
  if (month < 10) {
    month = '0' + month
  }
  if (day < 10) {
    day = '0' + day
  }
  return year + '-'+ month + '-' + day
}
console.log (formatDate (new Date ()))
```