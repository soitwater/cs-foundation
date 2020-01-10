# path

## 属性
- 获取路径：path.dirname(filepath)  `/tmp/demo/js/test.js 中的 /tmp/demo/js`
- 获取文件名：path.basename(filepath)  `/tmp/demo/js/test.js 中的 test.js `
- 获取扩展名：path.extname(filepath) `/tmp/demo/js/test.js 中的 js `
- 注意
  * 如果只想获取文件名，单不包括文件扩展呢?
  * `console.log( path.basename('/tmp/demo/js/test.js', '.js') );`
## path.normalize  
  - 将路径转化为标准模式(解析`./ ../`等,并去除多余斜杆,最后的斜杆方向因操作系统而异)
## path.join  
  - 链接多个路径为标准路径, 最后的斜杆方向因操作系统而异
## path.extname  
  - 返回文件名的后缀(带 `.` 号)
## path.resolve
  - 获取文件的绝对路径
## `path.format(pathObject)` 以及 `path.parse(filepath)`
- `path.format(pathObject)` 与 `path.parse(filepath)` 互为反向操作
-  参数
   * `root` vs `dir`：两者可以互相替换，区别在于，路径拼接时，`root`后不会自动加`/`，而`dir`会
   * `base` vs `name+ext`：两者可以互相替换
   * 参数对比
     - windows下
       ```bash
       ┌─────────────────────┬────────────┐
       │          dir        │    base    │
       ├──────┬              ├──────┬─────┤
       │ root │              │ name │ ext │
       " C:\      path\dir   \ file  .txt "
       └──────┴──────────────┴──────┴─────┘
       ```
     - linux 下
       ```bash
       ┌─────────────────────┬────────────┐
       │          dir        │    base    │
       ├──────┬              ├──────┬─────┤
       │ root │              │ name │ ext │
       "  /    home/user/dir / file  .txt "
       └──────┴──────────────┴──────┴─────┘
       ```
- 例子
  ```javascript
  path.parse('/home/user/dir/file.txt')
  // returns
  // {
  //    root : "/",
  //    dir : "/home/user/dir",
  //    base : "file.txt",
  //    ext : ".txt",
  //    name : "file"
  // }
  ```
  ```javascript
  var path = require('path');
  
  var p1 = path.format({
    root: '/tmp/', 
    base: 'hello.js'
  });
  console.log( p1 ); // 输出 /tmp/hello.js
  
  var p2 = path.format({
    dir: '/tmp', 
    name: 'hello',
    ext: '.js'
  });
  console.log( p2 );  // 输出 /tmp/hello.js
  ```
### path.relative(from, to)
- 从`from`路径，到`to`路径的相对路径。
- 边界：
  * 如果`from`、`to`指向同个路径，那么，返回空字符串。
  * 如果`from`、`to`中任一者为空，那么，返回当前工作路径。
- 例子：
  ```javascript
  var path = require('path');
  var p1 = path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb');
  console.log(p1);  // 输出 "../../impl/bbb"
  ```


## 参考
- [Node.j中path模块对路径的操作](https://www.cnblogs.com/duhuo/p/4752640.html)
