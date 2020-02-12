# 你不知道的JS上
JS是一门编译语言，而不是一门解释语言														
														
## 编译的3个步骤													
- 分词/词法分析：将字符串拆分成有意义的代码块(词法单元)，														
- 解析/语法分析：将词法单元流(数组)转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树(抽象语法树 AST)						
- 代码生成： 将AST转换为可执行的代码														
														
## 理解作用域需要知道3个概念
- 引擎(负责整个JS编译与执行的过程);  
- 编译器(负责编译的3个步骤); 
- 作用域(类似为所有的变量声明打上标识符, public, private, ...)														
														
## 变量的赋值过程(var a = 1)包括2个操作											
- 编译器在当前作用域声明一个变量(如果之前没声明过)														
- 运行时引擎会在作用域中查找该变量，如果能找到就会对它赋值														
- 注意：引擎在作用域中进行查找时，有两种方式 LHS(赋值操作的目标是谁) 以及 RHS(谁是赋值操作的源头)  
  例如 a = 2 是 LHS ; foo(2) 中 foo() 则是 RHS；   
  错误的理解：类似于"按值，还是按引用 " 的意思吗？即对"对象"是RHS，对"非对象"则是LHS的意思吗？	  																							
## 作用域嵌套														
在查找变量时，如果在当前作用域中不能查找到变量，会往外层作用域继续查找，直到最外层作用域；														
														
## 作用域类型														
① 词法作用域 （如 JS）  												
② 动态作用域（Bash脚本，Perl）														
- 词法作用域：在将代码词法化(编译过程的第①步)的阶段，根据变量被写在哪里，此时就已经确定变量作用域了；					
- 欺骗词法：词法作用域完全由写代码时变量所声明的位置所决定(写的时候就已经确定了, 写在哪, 哪里就是其作用域)，但有两种手段可以在运行时“修改”该变量的词法作用域																											
- 欺骗词法手段：① eval ② with 
  ```js	  													
  （欺骗词法作用域会导致性能下降, JS引擎会在编译阶段进行数项性能优化, 优化需要对代码进行静态分析,而 eval 以及 with 使引擎无法确定会接受到什么代码，无法进行优化）														
 
  eval 仅适用于"宽松模式"或"懒惰模式"，不适用于"严格模式"，在"严格模式"中，eval 无法在声明中修改其作用域	  
  with 通常被当作重复引用同一个对象中的多个属性的快捷方式，可以不需要重复引用对象本身														
  如： 														
  let obj = {														
    a: 1,														
    b: 2,														
  }																												
  obj.a = 3														
  obj.b = 4														
  // 等价于														
  with (obj) {														
    a = 3;														
    b = 4;														
  }														
  ```

## 函数作用域的含义：														
属于这个函数的全部变量都可以在整个函数的范围内使用及复用(事实上在嵌套的作用域中也可以使用)													
														
## JS中利用函数中嵌套函数，实现"私有"函数，体现了软件中的"最小暴露原则"														
														
## 区分函数声明和表达式最简单的方法
是看 function 关键字出现在声明中的位置（不仅仅是一行代码，而是整个声明中的位置）。  
如果 function 是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式。  
如： function a () {} // 就是一个函数声明	  
函数声明不能省略函数名称，函数表达式可以省略函数名称														
														
## var 与块作用域 
var 不支持块作用域，所以 小心 if () {} 以及 for () {} 里的var 声明的变量														
														
## 创建块作用域														
- with														
- try / catch : catch 分局里的变量只在 catch 块中起作用，catch分句会创建一个块作用域														
														
## 变量提升
变量声明会被视为存在于其所出现的作用域的整个范围内   
如下：变量 err 就没有提升，
```js
console.log(err) // 会报错  
console.log(err)  
let err = "Error" 
``` 

"提升"是优先提升函数，再提升变量（重复的声明会被忽略）	
```	js																									
foo(); // 1 (不是 2)														
var foo;														
function foo() {														
  console.log( 1 );														
}														
foo = function() {														
  console.log( 2 );														
};														
														
// 等价于														
function foo() {														
  console.log( 1 );														
}														
foo(); // 1														
foo = function() { // 这里的 var foo 因为 foo 已经被声明过了, 因此不再声明														
  console.log( 2 );														
};														
再如：不要尝试在 if 结构 里声明函数														
foo(); // "b"  因为先提升变函数量														
var a = true;														
if (a) {														
  function foo() { console.log("a"); }														
}														
else {														
  function foo() { console.log("b"); }														
}														
														
														
														
function foo() {														
  var a = 2;														
  function bar() {														
    console.log( a );														
  }														
  return bar;														
}														
var baz = foo();														
baz(); // 2 —— 朋友，这就是闭包的效果。														
函数 bar() 的词法作用域能够访问 foo() 的内部作用域。然后我们将 bar() 函数本身当作一个值类型进行传递。在这个例子中，我们将 bar 所引用的函数对象本身当作返回值。														
在 foo() 执行后，其返回值（也就是内部的 bar() 函数）赋值给变量 baz 并调用 baz() ，实际上只是通过不同的标识符引用调用了内部的函数 bar() 。														
bar() 显然可以被正常执行。但是在这个例子中，它在自己定义的词法作用域以外的地方执行。														
在 foo() 执行后，通常会期待 foo() 的整个内部作用域都被销毁，因为我们知道引擎有垃圾回收器用来释放不再使用的内存空间。由于看上去 foo() 的内容不会再被使用，所以很自然地会考虑对其进行回收。														
而闭包的“神奇”之处正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此没有被回收。谁在使用这个内部作用域？原来是 bar() 本身在使用。														
拜 bar() 所声明的位置所赐，它拥有涵盖 foo() 内部作用域的闭包，使得该作用域能够一直存活，以供 bar() 在之后任何时间进行引用。														
bar() 依然持有对该作用域的引用，而这个引用就叫作闭包。														
														
														
闭包使得函数可以继续访问定义时的词法作用域 --> 因此,导致：①闭包可以访问函数内部变量(类似访问私有变量) ②使函数内部变量一直存在于内存中														
只要使用了回调函数，其实就是在使用闭包。														
														
														
let 与 var 在 for 循环 中的一个重要区别在于：														
for (let a = 1; a < 10; a++) 中的 let a = ...是每次循环都会重新声明的（let 承认 for 循环 是一个块作用域）称为：隐式劫持一个已经存在的作用域														
for (var a = 1; a < 10; a++)  中的var a = … 不会重新声明, 因为 var a = … 在遇到所声明的变量已经存在时, 会忽略声明														
														
														
// 创建一个模块														
function CoolModule() {														
  var something = "cool";														
  var another = [1, 2, 3];														
  function doSomething() {														
    console.log( something );														
  }														
  function doAnother() {														
    console.log( another.join( " ! " ) );														
  }														
  return {														
    doSomething: doSomething,														
    doAnother: doAnother														
  };														
}														
														
var foo = CoolModule();														
foo.doSomething(); // cool														
foo.doAnother(); // 1 ! 2 ! 3														
														
														
// 单例模式 实现														
var foo = (function CoolModule() {														
  var something = "cool";														
  var another = [1, 2, 3];														
  function doSomething() {														
    console.log( something );														
  }														
  function doAnother() {														
    console.log( another.join( " ! " ) );														
  }														
  return {														
    doSomething: doSomething,														
    doAnother: doAnother														
  };														
})();														
foo.doSomething(); // cool														
foo.doAnother(); // 1 ! 2 ! 3														
														
														
块作用域的替代方案														
{														
  try {														
    throw undefined;														
  } catch (a) {														
    a = 2;														
    console.log( a );														
  }														
}														
														
var obj = {														
  id: "酷",														
  cool: function coolFn() {														
    console.log( this.id );														
  }														
};														
var id = "不酷"														
obj.cool(); // 酷  理由：obj.cool 相当于 一个指针，指向函数 coolFn，因此 obj.cool() 相当于 obj.coolFn ()														
setTimeout( obj.cool, 100 ); // 不酷  理由：相当于将 coolFn () 作为参数传入 setTimeout () 中，传的是 coolFn() 而不包括 obj 对象 （对吗？）														
														
使用 arguments.														
callee 来引用当前正在运行的函数对象。这是唯一一种可以从匿名函数对象														
内部引用自身的方法。														
														
当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。   
this 就是记录的其中一个属性，会在函数执行的过程中用到。	  													
														
														
要求当前词法作用域使用“严格模式”														
(function(){														
  use strict;														
  foo(); // 2														
})();														
```

## 变量提升 
- 变量提升针对的是`var`以及`function () {}`, 可以查看这篇文章[JavaScript解析机制——深入理解变量提升](https://blog.csdn.net/weixin_42779798/article/details/81180764)
- 一道面试做错的题目
  ```js
  var x = 5;
  function f() {
    console.log(x);   //undefined; 注意 function() {} 也是一个块级作用域; 变量提升最多提升到当前作用域的顶部,所以这里的 x 值为 undefined
    var x = 2;
  }
  f()
  ```
- 在同一个作用域下声明两个相同的变量或者函数，那么后一个会覆盖前一个
  ```js
  var f = 5;
  function f() {
    console.log("xx");
  }
  f();   //error: f is not a function; 因为
  ```
  ```js
  var f; // 如果变量m定义后没有赋值 ，那么函数就不会被覆盖了
  function f() {
    console.log("xx");
  }
  f()
  ```
  ```js
  console.log(x);   //function x() {console.log(5);}
  var x = 2;
  console.log(x);   //2
  function x() {
    console.log(3);
  }
  console.log(x);   //2
  var x = 3;
  console.log(x);   //3
  function x() {
    console.log(5);
  }
  console.log(x);   //3
  x();   // error: x is not a function
  /*上述代码相当于以下*/
  function x() {
    console.log(5);
  }
  var x
  console.log(x);   //function x() {console.log(5);}
  x = 2
  console.log(x);   // 2
  console.log(x);   // 2
  x = 3
  console.log(x);   // 3
  console.log(x);   // 3
  x();   // error: x is not a function  
  ```
- 暂时性死区  
  * 主要是针对 let 以及 const
  * 只要在块内存在let命令，那么这个变量就绑定到了当前块作用域，不再受外部变量的影响
    ```js
    var foo = 3;
    if (true) {
      foo = 5; // Uncaught ReferenceError
      let foo;
    }
    ```
  * let/const 也存在变量声明提升，只是没有初始化分配内存。 一个变量有三个操作，声明(提到作用域顶部)，初始化(赋默认值)，赋值(继续赋值)。
    - var 是一开始变量声明提升，然后初始化成 undefined，代码执行到那行的时候赋值。
    - let 是一开始变量声明提升，然后没有初始化分配内存，代码执行到那行初始化，之后对变量继续操作是赋值。因为没有初始化分配内存，所以会报错，这是暂时性死区。
## 块作用域非常有用的2个原因														
- 闭包														
- 回收内存垃圾的回收机制
```js														
function process(data) {														
  // 在这里做点有趣的事情														
}														
														
var someReallyBigData = {														
  // 几百MB的object类型的数据														
}														
process(someReallyBigData) // 这里数据比较大, 处理时会占用很高的内存														
														
var btn = document.getElementById( "my_button" )														
btn.addEventListener( "click", function click (evt) {														
  console.log("button clicked") // click() 不会调用到 someReallyBigData														
}, false )														
														
// 按理,process(someReallyBigData)执行完后,应该释放 someReallyBigData,空出内存														
// 但是,因为 btn.addEventListener 监听函数 click(event)														
// click(event) 会一直监听，且因为闭包, 引擎觉得在 click(event) 函数外的变量也可能随时会被使用, 														
// 因此 someReallyBigData 不会被释放														
解决方法：														
function process(data) {														
// 在这里做点有趣的事情														
}														
														
{														
var someReallyBigData = {														
// 几百MB的object类型的数据														
}														
process(someReallyBigData) // 这里数据比较大, 处理时会占用很高的内存														
}														
														
var btn = document.getElementById( "my_button" )														
btn.addEventListener( "click", function click (evt) {														
console.log("button clicked") // click() 不会调用到 someReallyBigData														
}, false )														
														
// 按理,process(someReallyBigData)执行完后,应该释放 someReallyBigData,空出内存														
// 但是,因为 btn.addEventListener 监听函数 click(event)														
// click(event) 会一直监听，且因为闭包, 引擎觉得在 click(event) 函数外的变量也可能随时会被使用, 														
// 因此 someReallyBigData 不会被释放														
														
														
console.log(a) // undefined														
var a = 2														
注意是输出 undefined，代码执行顺序是 var a ; console.log(a) ; a = 2; 不会报错 ReferenceError (这就是"提升")														
														
														
foo(); // TypeError														
bar(); // ReferenceError														
var foo = function bar() {														
  // ...														
};														
实际为：														
var foo;														
foo(); // TypeError														
bar(); // ReferenceError														
foo = function() {														
  var bar = ...self...														
  // ...														
}														
```														
														
## this
- 函数的调用方式其实只有`func.call(context, p1, p2, ...)`,其他方式都是这种形式`obj.childMethod.call(obj, ...args)`的变形  
- 全局函数,context 为 undefined
- 如：`arr = [fn1, fn2]`, 则`arr[0] () `相当于 `arr.0.call(arr)`, 这里的 this 是 arr


# 你不知道的JS中

## 第二章-回调
- 回调的真正缺陷：
  * 使用回调表达异步的方式(根据某一事件的执行结果,采取不同的回调,不同的回调结果又会有回调的回调)，难以被大脑所理解
  * 回调的信任问题(调用过早,调用过晚,回调未调用)
  * 嵌套与缩进虽然很有规律,但纵向的代码使阅读难以跳转

## 第三章-Promise