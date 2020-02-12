# JS_AST

## 编译的3个步骤
- 分词/词法分析：将字符串拆分成有意义的代码块(词法单元)，														
- 解析/语法分析：将词法单元流(数组)转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树(抽象语法树 AST)						
- 代码生成： 将AST转换为可执行的代码	
  
### 分词
- 将JS代码进行字符串处理，分类，并用`JSON`存储。例如：
  ```js
  // 原代码
  code = "var s = 'abc, def.'"
  // 分词之后
  [ 
    { type: 'identifier', value: 'var' },
    { type: 'identifier', value: 's' },
    { type: 'Punctuator', value: '=' },
    { type: 'string', value: '\'abc, def.\'' } 
  ]
  ```
### 生成AST
- 生成AST的过程非常复杂, 需要适配许多的情况，简单的字符串处理、正则处理并不能满足，这里可以借助依赖库实现（`esprima`）。
  ```js
  var esprima = require('esprima');
  var program = `
    const answer = 42; 
    function add (x, y) {
      return x + y
    }`;
  let result = esprima.tokenize(program)
  let ast = esprima.parseScript(program)
  console.log(result)
  console.log(ast)
  
  // 输出
  /*
  [
    { type: 'Keyword', value: 'const' },
    { type: 'Identifier', value: 'answer' },
    { type: 'Punctuator', value: '=' },
    { type: 'Numeric', value: '42' },
    { type: 'Punctuator', value: ';' },
    { type: 'Keyword', value: 'function' },
    { type: 'Identifier', value: 'add' },
    { type: 'Punctuator', value: '(' },
    { type: 'Identifier', value: 'x' },
    { type: 'Punctuator', value: ',' },
    { type: 'Identifier', value: 'y' },
    { type: 'Punctuator', value: ')' },
    { type: 'Punctuator', value: '{' },
    { type: 'Keyword', value: 'return' },
    { type: 'Identifier', value: 'x' },
    { type: 'Punctuator', value: '+' },
    { type: 'Identifier', value: 'y' },
    { type: 'Punctuator', value: '}' }
  ]
  */
  /*
  Script {
    type: 'Program',
    body:
     [ VariableDeclaration {
         type: 'VariableDeclaration',
         declarations: [Array],
         kind: 'const' },
       FunctionDeclaration {
         type: 'FunctionDeclaration',
         id: [Identifier],
         params: [Array],
         body: [BlockStatement],
         generator: false,
         expression: false,
         async: false } ],
    sourceType: 'script' 
  }
  */
  ```
- 还有另外两个可用的库
  * `estraverse` 可以遍历树的所有节点，并对相应的节点进行操作(例如:将`==`转化为`===`)
  * `escodegen` 可以把树再加工转成源代码

## 注意
- 下面的代码有`8 + 4 + 1`个`词`
  ```js
  function add (x, y) {
    return x + y
  }
  ```
- 手写`分词`
  ```js
  // 思路分析：
  // 传入的是字符串的参数，然后每次取一个字符去校验，用if语句去判断，
  // 然后最后结果存入一个数组中，对于标识符和数字进行特殊处理
  function tokenCode(code) {
    const tokens = [];
    //字符串的循环
    for (let i = 0; i < code.length; i++) {
      let currentChar = code.charAt(i);
      //是分号括号的情况
      if (currentChar === ';' || currentChar === '(' || currentChar === ')' || currentChar === '}' || currentChar === '{' || currentChar === '.' ||   currentChar === '=') {
        // 对于这种只有一个字符的语法单元，直接加到结果当中
        tokens.push({
          type: 'Punctuator',
          value: currentChar,
        });
        continue;
      }
      //是运算符的情况
      if (currentChar === '>' || currentChar === '<' || currentChar === '+' || currentChar === '-') {
        // 与上一步类似只是语法单元类型不同
        tokens.push({
          type: 'operator',
          value: currentChar,
        });
        continue;
      }
      //是双引号或者单引号的情况
      if (currentChar === '"' || currentChar === '\'') {
        // 引号表示一个字符传的开始
        const token = {
          type: 'string',
          value: currentChar,       // 记录这个语法单元目前的内容
        };
        tokens.push(token);
  
        const closer = currentChar;
  
        // 进行嵌套循环遍历，寻找字符串结尾
        for (i++; i < code.length; i++) {
          currentChar = code.charAt(i);
          // 先将当前遍历到的字符无条件加到字符串的内容当中
          token.value += currentChar;
          if (currentChar === closer) {
            break;
          }
        }
        continue;
      }
      if (/[0-9]/.test(currentChar)) {
        // 数字是以0到9的字符开始的
        const token = {
          type: 'number',
          value: currentChar,
        };
        tokens.push(token);
  
        for (i++; i < code.length; i++) {
          currentChar = code.charAt(i);
          if (/[0-9\.]/.test(currentChar)) {
            // 如果遍历到的字符还是数字的一部分（0到9或小数点）
            // 这里暂不考虑会出现多个小数点以及其他进制的情况
            token.value += currentChar;
          } else {
            // 遇到不是数字的字符就退出，需要把 i 往回调，
            // 因为当前的字符并不属于数字的一部分，需要做后续解析
            i--;
            break;
          }
        }
        continue;
      }
  
      if (/[a-zA-Z\$\_]/.test(currentChar)) {
        // 标识符是以字母、$、_开始的
        const token = {
          type: 'identifier',
          value: currentChar,
        };
        tokens.push(token);
  
        // 与数字同理
        for (i++; i < code.length; i++) {
          currentChar = code.charAt(i);
          if (/[a-zA-Z0-9\$\_]/.test(currentChar)) {
            token.value += currentChar;
          } else {
            i--;
            break;
          }
        }
        continue;
      }
  
      if (/\s/.test(currentChar)) {
        // 连续的空白字符组合到一起
        const token = {
          type: 'whitespace',
          value: currentChar,
        };
        // 与数字同理
        for (i++; i < code.length; i++) {
          currentChar = code.charAt(i);
          if (/\s]/.test(currentChar)) {
            token.value += currentChar;
          } else {
            i--;
            break;
          }
        }
        continue;
      }
      throw new Error('Unexpected ' + currentChar);
    }
    return tokens;
  }
  
  
  let code = "var s = 'abc, def.'"
  let tokens = tokenCode(code)
  console.log(tokens)
  ```


## 参考
- [30分钟学会JS AST，打造自己的编译器](https://www.jianshu.com/p/eff95b93bb8a)
- 