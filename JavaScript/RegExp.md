# RegExp

## 声明
1. `new RegExp("\\w+", "g") `  \w需要转义, 但也可以使用变量
2. `/\w+/g`  不需要转义, 但无法使用变量

## 修饰符
- i	执行对大小写不敏感的匹配。
- g	执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。
- m	执行多行匹配。表示锚`^`与`$`除了匹配字符串的开头与结尾,还可以匹配字符串每一行的开头与结尾

## 元字符
```
.	查找单个字符，除了换行和行结束符。
\w	查找单词字符。
\W	查找非单词字符。
\d	查找数字。
\D	查找非数字字符。
\s	查找空白字符。
\S	查找非空白字符。
\b	匹配单词边界。
\B	匹配非单词边界。

x|y     匹配x或者y中的一个
[a-z]   匹配a-z中的任意一个字符
[^a-z]  匹配非a-z中的任意一个字符
[xyz]   匹配x或y或z中的一个字符
[^xyz]  和上方相反

\0	查找 NUL 字符。
\n	查找换行符。
\f	查找换页符。
\r	查找回车符。
\t	查找制表符。
\v	查找垂直制表符。
\xxx	查找以八进制数 xxx 规定的字符。
\xdd	查找以十六进制数 dd 规定的字符。
\uxxxx	查找以十六进制数 xxxx 规定的 Unicode 字符。
```

## 量词
```
n+	    匹配任何包含至少一个 n 的字符串。
n*	    匹配任何包含零个或多个 n 的字符串。
n?	    匹配任何包含零个或一个 n 的字符串。
n{X}    匹配包含 X 个 n 的序列的字符串。
n{X,Y}	匹配包含 X 至 Y 个 n 的序列的字符串。 前后都是闭区间
n{X,}   匹配包含至少 X 个 n 的序列的字符串。
n$	    匹配任何结尾为 n 的字符串。
^n	    匹配任何开头为 n 的字符串。
?=n	    匹配任何其后紧接指定字符串 n 的字符串。(先行断言)举个简单例子：x(?=y)，如果x后面跟的是y，则匹配x。
?!n	    匹配任何其后没有紧接指定字符串 n 的字符串。(后行断言)
```

## 示例
```js
// replace
let myString = "ab AB cd CD"; 
let myRegexp = /AB/; 
let result = myString.replace(myRegexp, "replace");

// 如 /[^a-z\d]/i，不区分大小写地匹配非a-z，非1-9的字符（一个字符一个字符地匹配）

// test
let RegexpTest1 = /\d-\d{3}/g
let RegexpTest2 = /\d-\d\d\d/g
let str1 = "1-123"
let str2 = "1-1231"
console.log (RegexpTest1.test(str1)) // true
console.log (RegexpTest1.test(str2)) // false
```
```js
let regexp1 = /\b/g
let str1 = 'Hello ! My name is syc'
let str2 = str1.replace(regexp1, '#')
console.log(str2) // #Hello# ! #My# #name# #is# #syc#
// 注意，若首尾为“单词”，则首的前，尾的后将被视为单词分界
// 若首尾是符号，则首的前，尾的后不视为单词分界（如下示例，注意首尾）
let regexp1 = /\b/g
let str1 = '?Hello ! My name is syc.'
let str2 = str1.replace(regexp1, '#')
console.log(str2) // ?#Hello# ! #My# #name# #is# #syc#.
// 单词由字母，数字，下划线组成

// 单词分界 \b 常用于匹配一个单词
// 如 /\beag\b/gi , 这里匹配 eag 而不匹配 eagg 或 eeag
```

```js
// 正则表达式分组 ()
如  /\b(VB)?(Java)?Script\b/gi
上式匹配 单词JavaScript 或 单词 VBScript 或 VBJavaScript
为了避免匹配VBJavaScript，将正则修改为 /\b(VB|Java)Script\b/gi
```

```js
// 重用字符组
// 字符串中同一数字不能出现两次，找出出错位置并替换为ERROR
let myStr = "006,007,007,007,001,002,002" 
// 这里的 \1 就表示 重用第一个被()的正则;同理 \2 表示重用第二个被() 的正则
let myRegexp = /(\d+),\1/g  
let myStr2 = myStr.replace(myRegexp, "ERROR")
console.log(myStr2) // 006,ERROR,007,001,ERROR


引用正则
let regexp1 = /(\d{4})/g
let str1 = "2012,2011"
// 下文的 $1 表示引用，引用正则中第一个括号里的
console.log(str1.replace(regexp1, "year $1")) // year 2012,year 2011
```

```js
String方法：
正则表达式常与 split()  replace( ) search( ) match( )
search()返回找到的位置;  // 参数是正则表达式,返回的是位置,找不到则返回 -1
search()只找一个，类似inddexOf()
match(正则)以数组形式返回找到的所有文本
replace(正则表达式, 要替换的字符串)

RegExp方法：
JavaScript RegExp 对象有 3 个方法：test()、exec() 和 compile()。
(1) test() 方法用来检测一个字符串是否匹配某个正则表达式，如果匹配成功，返回 true ，否则返回 false；原理同 .exec()
(2) exec(正则) 方法用来检索字符串中与正则表达式匹配的值。exec() 方法返回一个数组，其中存放匹配的结果。如果未找到匹配的值，则返回 null；与String.match(正则非g)用法一致
(3) compile() 方法可以在脚本执行过程中编译正则表达式，也可以改变已有表达式。
注意：match是支持正则表达式的String对象的方法
```
`.match()`里的正则若不加`g`，则依旧返回数组,且result[0]为匹配结果, result[1]至result[n]匹配的是子表达式圆括号`()`里的结果,即`$1`到`$n`
```js
let quote1 = /[a-z]+/g; 
let quote2 = /([a-z])+(\d)/; 
let text = `a1b2c3`
console.log(text.match(quote1)) // [ 'a', 'b', 'c' ]
console.log(text.match(quote2)) // [ 'a1', 'a', '1', index: 0, input: 'a1b2c3', groups: undefined ]
```
`.test()`与`.exec()`中的正则若设置了全局`/略/g`,那么可以反复调用`.test()`与`.exec()`
```js
let quote1 = /java/g; 
let text = `java/java/java`
let result = []
while ((result = quote1.exec(text)) !== null) {
  console.log(result[0], result.index, quote1.lastIndex) // lastIndex 是 Regexp的默认属性, 表示下一次检索的起始位置
}
// 输出
java 0 4
java 5 9
java 10 14
```
### 关于Rexexp的实例属性lastIndex
- [JS中正则表达式要注意lastIndex属性](https://www.jb51.net/article/120608.htm)
- 每个RegExp对象都包含5个属性，source、global、ignoreCase、multiline、lastIndex。
  * source：是一个只读的字符串，包含正则表达式的文本。
  * `global、ignoreCase、multiline` 对应三种模式
  * lastIndex：是一个可读/写的整数，如果匹配模式中带有g修饰符，这个属性存储在整个字符串中下一次检索的开始位置，这个属性会被exec( ) 和 test( ) 方法用到。
- 例子：匹配句子`"we will we will rock you"`中`we will`后的第一个单词,正确答案应该是 `['we', 'rock']`,   
  但是，在第一次的操作中，假如不变动`regexp.lastIndex`属性，即使设置了模式`g`, 发现在第一次查找到`we will we`中的`we`时,   
  `lastIndex`会从`we will we`后的空格`' '`开始匹配,导致错过了`rock`
  因此加入了判断
  ```js
  // 此题出自 1078Bigram分词.js
  // exec 查询结果示例 [ 'we will we','we',index: 0,input: 'we will we will rock you', groups: undefined ]
  while(1) {
    let exec = regexp.exec(text)
    if (!exec) {
      break
    }
    arr.push(exec[1]) 
    regexp.lastIndex -= exec[1].length
  }
  ```


## 名词解释
### 圆括号()
- 将单独的项组合成子表达式
  * `/java(script)?/` 就和四则运算里的括号一样，这里匹配`java`与`javascript`
- 在完整的模式中定义子模式  
  * 当一个正则与目标字符串匹配时,可以从目标串中抽出与圆括号中的子模式相匹配的部分
  * `/[a-z]+(\d+)/`匹配一个或多个字母+一个或多个数字，并且可以从匹配结果中抽取出数字
- 圆括号允许在同一正则中的后部分引用前面部分的子表达式,`\1`表示第一个带圆括号的子表达式
  * 嵌套的子表达式怎么算是第几个  
    `([Aa]([Bb]))([Cc])`这里的`([Bb])`是`\2`  
  * 例子
    `/(['"])[^'"]*['"]/`这个正则匹配首尾单引号/双引号,中间不能有单引号/双引号(允许句首是单引号,句尾是双引号这种情况)
    `/(['"])[^'"]*\1/`这个正则因为`\1`会自动匹配第一个子表达式`(['"])`,因此匹配结果句首,句尾是相同的引号
  * 仅分组,取消引用
    `/(?:script)(fff)/`这里的`\1`是引用`(fff)`
- `(?:其他)`这个正则表达式的作用与`(其他)`的区别在于：前者无法使用`\2`这种带数字的编码的引用,会被跳过

### 番外`.replace()`
* 例子
  ```js
  let quote = /"([^"]*)"/g; // 匹配: 首尾必须是引号,中间的区域不能有引号
  let text = `"learn js"`
  console.log(text.replace(quote, `“$1”`)) // “learn js” 替换了引号的半角
  ```
* `$1`表示`.replace()`里正则的`([^"]*)`所匹配的内容
* `.replace()`的第二个参数还可以是函数
  - 例子
    ```js
    sUrl.replace(/\??(\w+)=(\w+)&?/g,function(a,k1,k2) {
      a 表示被匹配的字符串  \??(\w+)=(\w+)&?
      k1 表示被匹配的子字符串  (\w+) 第一个
      k2 表示被匹配的子字符串  (\w+) 第二个
    })
    ```
   
### 分组捕获
- 类似于将多个正则表达式写在一起，如`/ab|cd|ef/`,这个分组捕获可以匹配`ab`也可以匹配`cd`与`ef`  
- 分组时，顺序是从左到又，假如左边的匹配到了，右边的正则表达式就全部忽略了

### 贪婪与非贪婪
贪婪：默认，会尽可能匹配较长的
非贪婪：在量词后添加`?`，会匹配最短的
```js
let str="abcaxc"
let reg1 = /ab.*c/
let reg2 = /ab.*?c/
console.log(reg1.exec(str)) // ["abcaxc"]
console.log(reg2.exec(str)) // ["abc"]
```
但是,需要注意：非贪婪的匹配会尽可能匹配长度更短的这一说法并不准确,  
因为还需要考虑:  
正则表达式的模式匹配总是会寻找字符串中的第一个可能匹配的位置,即匹配是从字符串的第一个字符开始的(不考虑子串中更短的匹配)  
```js
let str = "aaab"
let reg1 = /a+?b/
console.log(reg1.exec(str)) // ["aaab"], 本以为会是['ab'],然而并不
```

### 懒惰性
即`/\d+/`与`/\d+/g`的区别

### 单词边界
大小写字母、数字、下划线为可以组成单词的字符，这些字符和其它字符相邻则为单词边界，  
其他边界则一律统称为`非单词边界`  
例子
```js
原字符串：   1234
单词边界：   \b 1 2 3 4 \b
非单词边界： 1 \B 2 \B 3 \B 4
```

### $1
$1 表示正则表达式里第一个小括号匹配到的内容  
不一定是`$1`这种写法,写作`first`也无所谓,只是`$1`是约定的写法

### 正则例子
```js
/^1[34578]\d{9}$/ // 手机号码: 11位数字(以1开头，第二位可能是3/4/5/7/8等的任意一个，在加上后面的\d表示数字[0-9]的9
/^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/ // Email: 邮箱格式检查
/^[a-zA-Z0-9_-]{4,16}$/ // 用户名: 4到16位（字母，数字，下划线，减号）
new RegExp("^([A-Z]|[a-z]|[0-9]|[`~!@#$%^&*()+=|{}':;',\\\\[\\\\].<>/?~！@#￥%……&*（）——+|{}【】‘；：”“'。，、？]){6,20}$", "gi") // 密码: 为6~20位, 由数字,英文,符号组成
```