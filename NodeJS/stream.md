# stream

- 与`buffer`的区别
  * buffer是拿到数据后放进缓冲区，缓冲区满了再一次性把数据拿出去处理
  * stream是拿到数据就处理，适用于大文件读取处理
- nodejs的核心模块，基本上都是stream的的实例，比如process.stdout、http.clientRequest。
  ```js
  const fs = require('fs');
  fs.createReadStream('./sample.txt').pipe(process.stdout);
  ```

## Stream分类
* Readable：用来读取数据，比如 `fs.createReadStream()`。
* Writable：用来写数据，比如 `fs.createWriteStream()`。
* Duplex：可读+可写，比如 `net.Socket()`。
* Transform：在读写的过程中，可以对数据进行修改，比如 `zlib.createDeflate()`（数据压缩/解压）。

### Readable Stream
* http.IncomingRequest
* fs.createReadStream()
* process.stdin
* 其他
* 例子一：
  ```js
  var fs = require('fs');
  fs.readFile('./sample.txt', 'utf8', function(err, content){
  	// 文件读取完成，文件内容是 [你好，我是程序猿小卡]
  	console.log('文件读取完成，文件内容是 [%s]', content);
  });
  ```
* 例子二：
  ```js
  var fs = require('fs');
  var readStream = fs.createReadStream('./sample.txt');
  var content = '';
  readStream.setEncoding('utf8');
  
  readStream.on('data', function(chunk){
  	content += chunk;
  });
  
  readStream.on('end', function(chunk){
  	// 文件读取完成，文件内容是 [你好，我是程序猿小卡]
  	console.log('文件读取完成，文件内容是 [%s]', content);
  });
  ```
* 例子三：
  - 这里使用了`.pipe(dest)`，好处在于，如果源文件较大，对于降低内存占用有好处。
    ```js
    var fs = require('fs');
    fs.createReadStream('./sample.txt').pipe(process.stdout);
    ```
  - 注意：这里只是原封不动的将内容输出到控制台，所以实际上跟前两个例子有细微差异。可以稍做修改，达到上面同样的效果
    ```js
    var fs = require('fs');
    var onEnd = function(){
    	process.stdout.write(']');	
    };
    var fileStream = fs.createReadStream('./sample.txt');
    fileStream.on('end', onEnd)
    fileStream.pipe(process.stdout);
    process.stdout.write('文件读取完成，文件内容是[');
    ```
    
### Writable Stream
- 同样以写文件为例子，比如想将`hello world`写到`sample.txt`里。
- 例子一：
  ```js
  var fs = require('fs');
  var content = 'hello world';
  var filepath = './sample.txt';
  
  fs.writeFile(filepath, content);
  ```
- 例子二：
  ```js
  var fs = require('fs');
  var content = 'hello world';
  var filepath = './sample.txt';
  
  var writeStram = fs.createWriteStream(filepath);
  writeStram.write(content);
  writeStram.end();
  ```
### Duplex Stream
- 例如`net.Socket`
- 例子
  服务端代码：
  ```js
  var net = require('net');
  var opt = {
  	host: '127.0.0.1',
  	port: '3000'
  };
  
  var server = net.createServer((socket) => {
      socket.on('data', (data) => {
          console.log('client send message: ', data.toString());
      });
      socket.write('hello client');
  });
  server.listen(opt.port, opt.host, ()=>{
      console.log(server.address());
  });
  ```
  
  客户端代码：
  ```js
  var net = require('net');
  var opt = {
  	host: '127.0.0.1',
  	port: '3000'
  };
  
  var client = net.connect(opt, function(){
  	client.write('msg from client');  // 可写
  });
  
  // 可读
  client.on('data', function(data){
    // lient: got reply from server [reply from server]
  	console.log('client: got reply from server [%s]', data);
  	client.end();
  });
  ```

### Transform Stream
- Transform stream是Duplex stream的特例，即Transform stream也同时可读可写。
- 跟Duplex stream的区别点在于，Transform stream的输出与输入是存在相关性的。
- 常见的Transform stream包括`zlib`、`crypto`，这里举个简单例子：文件的gzip压缩。
- 例子
  ```js
  var fs = require('fs');
  var zlib = require('zlib');
  
  var gzip = zlib.createGzip();
  
  var inFile = fs.createReadStream('./extra/fileForCompress.txt');
  var out = fs.createWriteStream('./extra/fileForCompress.txt.gz');
  
  inFile.pipe(gzip).pipe(out);
  ```

## 参考
- [程序猿小卡](https://github.com/chyingp/nodejs-learning-guide)