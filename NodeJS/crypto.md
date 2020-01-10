# crypto
- 与摘要、加密、签名相关
- 一些术语
  ```js
  MD5：Message-Digest Algorithm 5，信息-摘要算法。
  SHA：Secure Hash Algorithm，安全散列算法。
  HMAC：Hash-based Message Authentication Code，密钥相关的哈希运算消息认证码。
  对称加密：比如AES、DES
  非对称加密：比如RSA、DSA
  ```
- 摘要
  * 常见的摘要算法
    - MD5：128位
    - SHA-1：160位
    - SHA256 ：256位
    - SHA512：512位
- 消息认证码
  * MAC（Message Authentication Code）：消息认证码，用以保证数据的完整性。运算结果取决于消息本身、秘钥。
  * MAC可以有多种不同的实现方式，比如HMAC。
  * HMAC（Hash-based Message Authentication Code）：可以粗略地理解为带秘钥的hash函数。
  * 其实就是`MD5`
- 加密
  * 作用：
    1. 确认信息来源于特定的主体。
    2. 确认信息完整、未被篡改。
  * 流程
    1. 发送方：生成签名。
       - 计算原始信息的摘要。
       - 通过私钥对摘要进行签名，得到电子签名。
       - 将原始信息、电子签名，发送给接收方。
    2. 接收方：验证签名。
       - 通过公钥解开电子签名，得到摘要D1。（如果解不开，信息来源主体校验失败）
       - 计算原始信息的摘要D2。
       - 对比D1、D2，如果D1等于D2，说明原始信息完整、未被篡改。
- 分组加密: 先将明文切分成固定长度的块（128位），再分别进行加密
- 对比非对称加密：
  1. 加密/解密：公钥加密，私钥解密。
  2. 签名/验证：私钥签名，公钥验证。
- 对称加密与非对称加密的对比
  1. 对称加密速度要快于非对称加密。
  2. 非对称加密通常用于加密短文本，对称加密通常用于加密长文本。
  3. 两者可以结合起来使用，比如HTTPS协议，可以在握手阶段，通过RSA来交换生成非对称秘钥。在之后的通讯阶段，可以使用对称加密算法对数据进行加密，秘钥则是握手阶段生成的。 


## 摘要算法
### sha256例子
```js
var crypto = require('crypto');
var fs = require('fs');

var content = fs.readFileSync('./test.txt', {encoding: 'utf8'});
var hash = crypto.createHash('sha256');
var output;

// hash.update(data[, input_encoding])：input_encoding可以是`utf8`、`ascii`或者`latin1`。
// 如果data是字符串，且没有指定 input_encoding，则默认是`utf8`。
// 注意，hash.update()方法可以调用多次
hash.update(content);

// hash.digest([encoding])：计算摘要。encoding可以是`hex`、`latin1`或者`base64`
// 如果声明了encoding，那么返回字符串。否则，返回Buffer实例。
// 注意，调用hash.digest()后，hash对象就作废了，再次调用就会出错。
output = hash.digest('hex'); 

console.log(output);
// 输出内容为：
// b94d27b9934d3e08a52e52d7da7dabfac484efe37a5380ee9088f7ace2efcde9
```
也可以这样：
```js
var crypto = require('crypto');
var fs = require('fs');

var input = fs.createReadStream('./test.txt', {encoding: 'utf8'});
var hash = crypto.createHash('sha256');

hash.setEncoding('hex');

input.pipe(hash).pipe(process.stdout)

// 输出内容为：
// b94d27b9934d3e08a52e52d7da7dabfac484efe37a5380ee9088f7ace2efcde9
```
hash.digest()后，再次调用digest()或者update(), 会报错
```js
var crypto = require('crypto');
var fs = require('fs');
var content = fs.readFileSync('./test.txt', {encoding: 'utf8'});
var hash = crypto.createHash('sha256');
var output;

hash.update(content);
hash.digest('hex'); 
// 报错：Error: Digest already called
hash.update(content);
// 报错：Error: Digest already called
hash.digest('hex');
```

### MD5加密例子
  ```js
  var crypto = require('crypto');

  function cryptPwd(password) {
    var md5 = crypto.createHash('md5');
    return md5.update(password).digest('hex');
  }
  
  var password = '123456';
  var cryptedPassword = cryptPwd(password);
  
  console.log(cryptedPassword);
  // 输出：e10adc3949ba59abbe56e057f20f883e
  ```

### MD5密码加盐
- 加盐时，应避免短盐值以及盐值固定。因此，需要`随机加盐`
  ```js
  var crypto = require('crypto');

  function getRandomSalt(){
    return Math.random().toString().slice(2, 5);
  }
  
  function cryptPwd(password, salt) {
    // 密码“加盐”
    var saltPassword = password + ':' + salt;
    console.log('原始密码：%s', password);
    console.log('加盐后的密码：%s', saltPassword);

    // 加盐密码的md5值
    var md5 = crypto.createHash('md5');
    var result = md5.update(saltPassword).digest('hex');
    console.log('加盐密码的md5值：%s', result);
  }
  
  var password = '123456';
  
  cryptPwd('123456', getRandomSalt());
  // 输出：
  // 原始密码：123456
  // 加盐后的密码：123456:498
  // 加盐密码的md5值：af3b7d32cc2a254a6bf1ebdcfd700115
  
  cryptPwd('123456', getRandomSalt());
  // 输出：
  // 原始密码：123456
  // 加盐后的密码：123456:287
  // 加盐密码的md5值：65d7dd044c2db64c5e658d947578d759
  ```

### MD5碰撞
- 两段不同的字符串，经过MD5运算后，得出相同的结果

### HMAC例子
例子1：
```js
var crypto = require('crypto');
var fs = require('fs');

var secret = 'secret';
var hmac = crypto.createHmac('sha256', secret);
var input = fs.readFileSync('./test.txt', {encoding: 'utf8'});

hmac.update(input);

console.log( hmac.digest('hex') );
// 输出：
// 734cc62f32841568f45715aeb9f4d7891324e6d948e4c6c60c0621cdac48623a
```
例子2：
```js
var crypto = require('crypto');
var fs = require('fs');

var secret = 'secret';
var hmac = crypto.createHmac('sha256', secret);
var input = fs.createReadStream('./test.txt', {encoding: 'utf8'});

hmac.setEncoding('hex');

input.pipe(hmac).pipe(process.stdout)
// 输出：
// 734cc62f32841568f45715aeb9f4d7891324e6d948e4c6c60c0621cdac48623a
```


## DH算法 - 密钥交换算法之一
- Diffie-Hellman（简称DH），它的作用是保证通信双方在非安全的信道中安全地交换密钥。
- 目前DH最重要的应用场景之一，就是在HTTPS的握手阶段，客户端、服务端利用DH算法交换对称密钥。
- DH算法结论
  1. 假设 `Y = a^X mod p`，已知X的情况下，很容易算出Y；已知道Y的情况下，很难算出X；
  2. `(a^Xa mod p)^Xb mod p = a^(Xa * Xb) mod p`
- 握手步骤说明
   - 假设客户端、服务端挑选两个素数a、p（都公开），然后
     * 客户端：选择自然数Xa，Ya = a^Xa mod p，并将Ya发送给服务端；
     * 服务端：选择自然数Xb，Yb = a^Xb mod p，并将Yb发送给客户端；
     * 客户端：计算 Ka = Yb^Xa mod p
     * 服务端：计算 Kb = Ya^Xb mod p
       ```
       Ka = Yb^Xa mod p 
         = (a^Xb mod p)^Xa mod p 
         = a^(Xb * Xa) mod p
         = (a^Xa mod p)^Xb mod p
         = Ya^Xb mod p
         = Kb
       ```
   - 可以看到，尽管客户端、服务端彼此不知道对方的Xa、Xb，但算出了相等的secret。
- Nodejs代码示例
  * 要点之一就是client、server采用相同的素数a、p。
  ```javascript
  var crypto = require('crypto');
  
  var primeLength = 1024;  // 素数p的长度
  var generator = 5;  // 素数a
  
  // 创建客户端的DH实例
  var client = crypto.createDiffieHellman(primeLength, generator);
  // 产生公、私钥对，Ya = a^Xa mod p
  var clientKey = client.generateKeys();
  
  // 创建服务端的DH实例，采用跟客户端相同的素数a、p
  var server = crypto.createDiffieHellman(client.getPrime(), client.getGenerator());
  // 产生公、私钥对，Yb = a^Xb mod p
  var serverKey = server.generateKeys();
  
  // 计算 Ka = Yb^Xa mod p
  var clientSecret = client.computeSecret(server.getPublicKey());
  // 计算 Kb = Ya^Xb mod p
  var serverSecret = server.computeSecret(client.getPublicKey());
  
  // 由于素数p是动态生成的，所以每次打印都不一样
  // 但是 clientSecret === serverSecret
  console.log(clientSecret.toString('hex'));
  console.log(serverSecret.toString('hex'));
  ```

## 加密、解密
- 加解密主要用到下面两组方法：
  * 加密：
    - crypto.createCipher(algorithm, password)
    - crypto.createCipheriv(algorithm, key, iv)
  * 解密：
    - crypto.createDecipher(algorithm, password)
    - crypto.createDecipheriv(algorithm, key, iv)
### crypto.createCipher(algorithm, password)
* algorithm：加密算法，比如`aes192`，具体有哪些可选的算法，依赖于本地`openssl`的版本，可以通过`openssl list-cipher-algorithms`命令查看支持哪些算法。
* password：用来生成密钥(key)、初始化向量(IV)。
```js
var crypto = require('crypto');
var secret = 'secret';

var cipher = crypto.createCipher('aes192', secret);
var content = 'hello';
var cryptedContent;

cipher.update(content);
cryptedContent = cipher.final('hex');
console.log(cryptedContent);
// 输出：
// 71d30ec9bc926b5dbbd5150bf9d3e5fb
```
### crypto.createDecipher(algorithm, password)
- crypto.createCipher(algorithm, password) 逆向操作
  ```js
  var crypto = require('crypto');
  var secret = 'secret';
  
  var cipher = crypto.createCipher('aes192', secret);
  var content = 'hello';
  var cryptedContent;
  
  cipher.update(content);
  cryptedContent = cipher.final('hex');
  console.log(cryptedContent);
  // 输出：
  // 71d30ec9bc926b5dbbd5150bf9d3e5fb
  
  var decipher = crypto.createDecipher('aes192', secret);
  var decryptedContent;
  
  decipher.update(cryptedContent, 'hex');
  decryptedContent = decipher.final('utf8');
  console.log(decryptedContent);
  // 输出：
  // hello
  ```

### crypto.createCipheriv(algorithm, key, iv)

相对于 crypto.createCipher() 来说，crypto.createCipheriv() 需要提供`key`和`iv`，而 crypto.createCipher() 是根据用户提供的 password 算出来的。

key、iv 可以是Buffer，也可以是utf8编码的字符串，这里需要关注的是它们的长度：

* key：根据选择的算法有关，比如 aes128、aes192、aes256，长度分别是128、192、256位（16、24、32字节）
* iv：都是128位（16字节）

```js
var crypto = require('crypto');
var key = crypto.randomBytes(192/8);
var iv = crypto.randomBytes(128/8);
var algorithm = 'aes192';

function encrypt(text){
    var cipher = crypto.createCipheriv(algorithm, key, iv);
    cipher.update(text);
    return cipher.final('hex');
}

function decrypt(encrypted){
    var decipher = crypto.createDecipheriv(algorithm, key, iv);
    decipher.update(encrypted, 'hex');
    return decipher.final('utf8');
}

var content = 'hello';
var crypted = encrypt('hello');
console.log( crypted );

var decrypted = decrypt( crypted );
console.log( decrypted );  // 输出：utf8
```




## 数字签名/签名校验
- 假设
  1. 服务端原始信息为M，摘要算法为Hash，Hash(M)得出的摘要是H。
  2. 公钥为Pub，私钥为Piv，非对称加密算法为Encrypt，非对称解密算法为Decrypt。
  3. Encrypt(H)得到的结果是S。
  4. 客户端拿到的信息为M1，利用Hash(M1)得出的结果是H1。
- 数字签名的产生、校验步骤分别如下：
  1. 数字签名的产生步骤：利用摘要算法Hash算出M的摘要，即Hash(M) == H，利用非对称加密算法对摘要进行加密Encrypt( H, Piv )，得到数字签名S。
  2. 数字签名的校验步骤：利用解密算法D对数字签名进行解密，即Decrypt(S) == H，计算M1的摘要 Hash(M1) == H1，对比 H、H1，如果两者相同，则通过校验。
- 代码示例
  ```js
  var crypto = require('crypto');
  var fs = require('fs');
  var privateKey = fs.readFileSync('./private-key.pem');  // 私钥
  var publicKey = fs.readFileSync('./public-key.pem');  // 公钥
  var algorithm = 'RSA-SHA256';  // 加密算法 vs 摘要算法
  
  // 数字签名
  function sign(text){
    var sign = crypto.createSign(algorithm);
    sign.update(text);
    return sign.sign(privateKey, 'hex');    
  }
  
  // 校验签名
  function verify(oriContent, signature){
    var verifier = crypto.createVerify(algorithm);
    verifier.update(oriContent);
    return verifier.verify(publicKey, signature, 'hex');
  }
  
  // 对内容进行签名
  var content = 'hello world';
  var signature = sign(content);
  console.log(signature);
  
  // 校验签名，如果通过，返回true
  var verified = verify(content, signature);
  console.log(verified);
  ```

## 参考
- [github - chyingp](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/cluster.md)