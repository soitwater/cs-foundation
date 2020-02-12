# Math

## 获取随机数且要求是长度一致的字符串格式
```js
function getRandom () {
  var r = Math.random ()
  r = r + '0000000000' // 在后面加上10个0
  r = r.slice(0, 10) // 只取前10位，多余的0则舍去
  console.log (r)
  return r
}
```

