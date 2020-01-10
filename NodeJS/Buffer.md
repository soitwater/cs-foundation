
# Buffer
- 将JS对字符串的操作拓展到二进制文件
- 构造Buffer
  * var bin = new Buffer([ 0x68, 0x65, 0x6c, 0x6c, 0x6f ]);
  * var bin = new Buffer("hello");

- Buffer转字符串
  `buffer.toString("utf-8")`
  
- 特性
  * 与字符串类似，含`.length`属性 
  * 与字符串类似，支持`buffer[index]`
  * JS对字符串的修改都是返回新字符串(原字符串不变)，对Buffer的修改则会改变原字符串，故拷贝buffer时需要注意
- api
  * 创建Buffer
    - new Buffer(array)
    - Buffer.alloc(length)
    - Buffer.allocUnsafe(length)
    - Buffer.from(str[, encoding])    默认的字符编码是'utf8'
      ```js
      const buf = new Buffer([0x62, 0x75, 0x66, 0x66, 0x65, 0x72]);
      var buf1 = Buffer.alloc(10);  // 长度为10的buffer，初始值为0x0
      var buf2 = Buffer.alloc(10, 1);  // 长度为10的buffer，初始值为0x1
      var buf3 = Buffer.allocUnsafe(10);  // 长度为10的buffer，初始值不确定
      var buf4 = Buffer.from([1, 2, 3])  // 长度为3的buffer，初始值为 0x01, 0x02, 0x03
      ```
  * 转常规字符串
    ```js
    const buf = new Buffer([0x62, 0x75, 0x66, 0x66, 0x65, 0x72])
    console.log(buf.toString()) // 默认编码方式是 utf8
    ```
  * 字符串比较
    - `buf.equals()`
      ```js
      // 例子三：编码不一样，内容相同
      var buf5 = Buffer.from('ABC');  // <Buffer 41 42 43>
      var buf6 = Buffer.from('414243', 'hex');
      console.log(buf5.equals(buf6));    //true
      ```
    - `buf.compare(target[, targetStart[, targetEnd[, sourceStart[, sourceEnd]]]])`
      ```js
      // 区别在于可以指定比较的范围
      // 返回值也不同：
      // 0：buf、target大小相同。
      // 1：buf大于target，也就是说buf应该排在target之后。
      // -1：buf小于target，也就是说buf应该排在target之前。
      ```
  * `Buffer.concat(list[, totalLength])`
    ```js
    // totalLength 参数为新连接后的Buffer的长度（多则补，少则裁剪）
    var buff4 = Buffer.from([1, 2]);
    var buff5 = Buffer.from([3, 4]);
    
    var buff6 = Buffer.concat([buff4, buff5], 5);
    
    console.log(buff6.length);  // 5
    console.log(buff6);  // <Buffer 01 02 03 04 00>
    
    var buff7 = Buffer.concat([buff4, buff5], 3);
    console.log(buff7.length);  // 3
    console.log(buff7);  // <Buffer 01 02 03>
    ```
  - 拷贝：`buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])`
    * 使用比较简单，如果忽略后面三个参数，那就是将buf的数据拷贝到target里去，如下所示：
    * 例子
      ```js
      var buff1 = Buffer.from([1, 2]);
      var buff2 = Buffer.alloc(2);
      buff1.copy(buff2);
      console.log(buff2);  // <Buffer 01 02>
      ```
      ```js
      const buf1 = Buffer.allocUnsafe(26);
      const buf2 = Buffer.allocUnsafe(26).fill('!');
      
      for (let i = 0 ; i < 26 ; i++) {
        // 97 is the decimal ASCII value for 'a'
        buf1[i] = i + 97;
      }
      
      buf1.copy(buf2, 8, 16, 20);
      
      // Prints: !!!!!!!!qrst!!!!!!!!!!!!!
      console.log(buf2.toString('ascii', 0, 25));
      ```
  - 查找：`buf.indexOf(value[, byteOffset][, encoding])`
    * 跟数组的查找差不多，需要注意的是，value可能是String、Buffer、Integer中的任意类型。
    * String：如果是字符串，那么encoding就是其对应的编码，默认是utf8。
    * Buffer：如果是Buffer实例，那么会将value中的完整数据，跟buf进行对比。
    * Integer：如果是数字，那么value会被当做无符号的8位整数，取值范围是0到255。
    * 例子
      ```js
      // byteOffset - 指定起始查找位置
      const buf = Buffer.from('this is a buffer');
      
      // Prints: 0
      console.log(buf.indexOf('this'));
      
      // Prints: 2
      console.log(buf.indexOf('is'));
      
      // Prints: 8
      console.log(buf.indexOf(Buffer.from('a buffer')));
      
      // Prints: 8
      // (97 is the decimal ASCII value for 'a')
      console.log(buf.indexOf(97));
      
      // Prints: -1
      console.log(buf.indexOf(Buffer.from('a buffer example')));
      
      // Prints: 8
      console.log(buf.indexOf(Buffer.from('a buffer example').slice(0, 8)));
      
      
      const utf16Buffer = Buffer.from('\u039a\u0391\u03a3\u03a3\u0395', 'ucs2');
      
      // Prints: 4
      console.log(utf16Buffer.indexOf('\u03a3', 0, 'ucs2'));
      
      // Prints: 6
      console.log(utf16Buffer.indexOf('\u03a3', -4, 'ucs2'));
      ```
  - 写：`buf.write(string[, offset[, length]][, encoding])`
    * 将sring写入buf实例，同时返回写入的字节数。
    * 参数如下：
      - string：写入的字符串。
      - offset：从buf的第几位开始写入，默认是0。
      - length：写入多少个字节，默认是 buf.length - offset。
      - encoding：字符串的编码，默认是utf8。
    * 例子
      ```js
      var buff = Buffer.alloc(4);
      buff.write('a');  // 返回 1
      console.log(buff);  // 打印 <Buffer 61 00 00 00>
      
      buff.write('ab');  // 返回 2
      console.log(buff);  // 打印 <Buffer 61 62 00 00>
      ```
  - 填充：`buf.fill(value[, offset[, end]][, encoding])`
    * 用`value`填充buf，常用于初始化buf。参数说明如下：
      - value：用来填充的内容，可以是Buffer、String或Integer。
      - offset：从第几位开始填充，默认是0。
      - end：停止填充的位置，默认是 buf.length。
      - encoding：如果`value`是String，那么为`value`的编码，默认是utf8。
    * 例子：
      ```js
      var buff = Buffer.alloc(20).fill('a');
      console.log(buff.toString());  // aaaaaaaaaaaaaaaaaaaa
      ```
  - 转成JSON字符串：`buf.toJSON()`
    ```js
    var buff = Buffer.from('hello');
    console.log( buff.toJSON() );  // { type: 'Buffer', data: [ 104, 101, 108, 108, 111 ] }
    ```
  - 遍历：`buf.values()、buf.keys()、buf.entries()`
    * 用于对`buf`进行`for...of`遍历，直接看例子。
      ```js
      var buff = Buffer.from('abcde');
      
      for(const key of buff.keys()){
          console.log('key is %d', key);
      }
      // key is 0
      // key is 1
      // key is 2
      // key is 3
      // key is 4
      
      for(const value of buff.values()){
          console.log('value is %d', value);
      }
      // value is 97
      // value is 98
      // value is 99
      // value is 100
      // value is 101
      
      for(const pair of buff.entries()){
          console.log('buff[%d] === %d', pair[0], pair[1]);
      }
      // buff[0] === 97
      // buff[1] === 98
      // buff[2] === 99
      // buff[3] === 100
      // buff[4] === 101
      ```
  - 截取：`buf.slice([start[, end]])`
    * 用于截取buf，并返回一个新的Buffer实例。需要注意的是，这里返回的Buffer实例，指向的仍然是buf的内存地址，所以对新Buffer实例的修改，也会影响到buf。
      ```js
      var buff1 = Buffer.from('abcde');
      console.log(buff1);  // <Buffer 61 62 63 64 65>
      
      var buff2 = buff1.slice();
      console.log(buff2);  // <Buffer 61 62 63 64 65>
      
      var buff3 = buff1.slice(1, 3);
      console.log(buff3);  // <Buffer 62 63>
      
      buff3[0] = 97;  // parseInt(61, 16) ==> 97
      console.log(buff1);  // <Buffer 62 63>
      ```
