# chunk
打包后生成的每一个js文件就是一个`chunk`

## 意义
```js
optimization: {
  minChunks: 2,
}
// 假设打包生成10个js文件，其中 lodash.js 仅仅被引用了1次
// 但是配置中 minChunks: 2, 即至少两个js文件引用了 lodash.js 才会去打包lodash.js
// 因此 lodash.js 文件不会被打包
