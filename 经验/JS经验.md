# JS经验
1. 在使用 `JSON.parse()` 注意判断字符串的格式：  
   有时对string需要使用不少于1次的 `JSON.parse()` 才能将 string 转化为 Object
2. ```js
   function inMethod (){ 
     setTimeout (()=> '1', 2000)
   }
   function outMethod (){ 
     return inMethod()
   }
   let test = outMethod()
   console.log(test) // 输出 undefined
   ```
3. ```js
   function Person() {}
     Person.prototype = {
       add: function () {
         alert(this.name);
       },
       name: "sy"
     }
   var p2 = new Person();
   p2.add();//第一种情况：sy 不报错 
   
   function Person(){};
   var p2 =new Person();
   Person.prototype = {
     add: function () {
       alert(this.name);
     },
     name :"ty"
   };
   p2.add();//第2种情况：error
   

   // 区分以上两种情况：在创建实例之前，原型链是否已经修改?
   ```
4. ```js
   typeof  1; //'number'
   typeof (1);//'number'
   typeof (); //SyntaxError 语法错误
   void  0; //undefined
   void (0);//undefined
   void (); //SyntaxError 语法错误，因此 void 是 一元运算符，此处将其当作 函数来使用了
   void 后面跟着任意类型的表达式，该表达式可以带括号，也可以不带；void只会返回 undefined（用于替代直接声明    undefined，  因为 undefined 不是 js 的关键字）
   ```
5. ```js
   let a = 0;  let b = 10; a = b++ - 1;  
   结果为 a = 9(不是11)， b=11；						
   但操作流程是 var temp = b - 1; b = b + 1; a = temp; 是最后才赋值的。 						
   ```
6. ```js
   let k = prompt("问点什么？", 49);
   k的值默认为 “49”，是字符串
   ```
7. ```js
   output(typeof (function() {
     output(“Hello World!”)
   })());
   ```
   上述代码输出：Hello World! Undefined  
   自执行函数执行后无返回值, 故输出 undefined
8. ```js
    console.log(1+ +"2"+"2");
   ```
   第一个+"2"中的加号是一元加操作符，+"2"会变成数值2，因此1+ +"2"相当于1+2=3.  
   然后和后面的字符串“2”相合并，变成了字符串"32".  
   除了 + 号，其他的运算符号都会强制使用 Number()  
   而 + 号则是在存在 一个加数是字符串的时候，会变成字符串拼接的功能  
9. setTimeout是window的一个方法，不是JS 的全局函数  
    - JavaScript中只有global全局对象，并没有window全局对象，window对象是浏览器厂商实现的，所以window对象下的setTimeOut方法不能算作JavaScript的全局函数。
10. 装箱
    - JS 中值的类型分为原始值类型和对象类型。
      * 原始值类型包括 number, string, boolean, null 和 undefined；
      * 对象类型即 object。首先原始值类型它就不是对象。
    - 另外，要注意 'hello' 和 new String('hello') 的区别，前者是字符串字面值，属于原始类型，而后者是对象。用 typeof 运算符返回的值也是完全不一样的：
      * typeof 'hello';  // 'string'
      * typeof new String('hello');  // 'object'
    - 当执行 'hello'.length 时，发现可以意料之中的返回 5，就觉得 'hello' 就是 String 对象，不然它怎么会有 String 对象的属性。
    - 其实，这是由于 JS 在执行到这条语句的时候，内部将 'hello' 包装成了一个 String 对象，执行完后，再把这个对象丢弃了，这种语法叫做 “装箱”，在其他面向对象语言里也有（如 C#）。
11. 变量回收
    ```js
    var i = 1;
    var i = 2;
    var add = function() {
      var i = 0;
      return function() {
        i++;
        console.log(i);
      }
    }();
    add();
    ```
    - 代码中哪几个变量没有被回收？——第2个 i，var add,  add() 中的 i
    - 首先是全局变量中的i，第二行的声明会忽略，但赋值会覆盖掉第一行，因此只有1个。
    - 第二个是var add，这个变量也没有回收，他定义了一个匿名函数，并将它赋给了add。
    - 第三个就是闭包中的变量i，闭包中的局部变量是不会被回收的，因此是3个变量没有被回收。
12. this
    ```js
    var myObject = {
      foo: "bar",
      func: function() {
        var self = this;
        console.log(this.foo);  
        console.log(self.foo);  
        (function() {
          console.log(this.foo);  
          console.log(self.foo);  
        }());
      }
    };
    myObject.func();  // 依次输出  bar  bar  undefined bar
    ```
    - 这里的 myObject.func();  是 myObject 调用 func () 的意思，并非说 myObject.func 就指向了一个地址(即func () )，再直接调用 func()
    - 立即执行匿名函数表达式（IIFE）是由window调用的，this指向 window
    - 像this 是一个保留字，看到它时，应该时刻注意此刻被调用时所处的词法作用域；
    - 而var self = this; 本质上self是this的副本，但也只是一个变量。在看到 self 时，若找不到self，则到上一层中去找self
13. onload是dom加载完触发, onresize是bom的函数
14. DOM中符合3C标准的事件对象
    - preventDefault()        取消事件默认行为
    - stopImmediatePropagation() 取消事件冒泡同时阻止当前节点上的事件处理程序被调用。
    - stopPropagation()      取消事件冒泡对当前节点无影响。
15. 保留字，关键字（所有的保留字都是小写的）
    ```js
    break     delete    function    return    typeof
    case      do        if          switch    var
    catch     else      in          this      void
    continue  false     instanceof  throw     while
    debugger  finally   new         true      with  
    default   for       null        try
    implements let private public yield interface package protected static
    ```
    ```js
    全局函数名
    arguments             Error             Math            String    
    Array                 eval              NaN             super
    Boolean               EvalError         Number          synchronized
    Date                  Function          Object          throws
    decodeURI             Infinity          parseFloat      transient
    decodeURIComponent    isFinite          parseInt        volatile
    encodeURI             isNaN             RangeError
    encodeURIComponent    JSON              regExp
    ```
16. 若你想用原生JS实现某个API，请参考[MDN](https://developer.mozilla.org/zh-CN/)里的polyfill
17. JS跳到指定位置[JS控制div跳转到指定的位置的几种解决方案总结](https://www.jb51.net/article/96574.htm)
    ```js
    // 法1
    window.scrollTo(100,500)
    // 法2
    document.getElementById('#xxx' ).scrollIntoView(true);
    ```
18. 在win10下，为了使Git Bash支持上下箭头，不用 `vue create 项目名称`而是使用`winpty vue.cmd create 项目名称`  
    * 推荐可以使用`vue ui`可视化的构建项目  
    * [vue-cli 3.0 UI控制台初体验](https://segmentfault.com/a/1190000015366009)
    * ## vue 创建项目报错   
      - `command failed: npm install --loglevel error --registry=https://registry.npm.taobao.org --disturl=https://npm.taobao.org/dist`
      - 找到文件`C:\Users\CSY\.vuerc`,找到属性`useTaobaoRegistry`其值修改为`true`
      - `npm cache clean --force` 清除npm的缓存
      - 升级 vue-cli / vue / npm / nodejs :   
        * `npm install -g @vue/cli`
        * `cnpm i vue -g`
        * n 是 nodejs 管理版本的一个特定的模块（n模块仅在 Linux 系统适用）
          - `npm install -g  --force n`
          - 稳定版 ：`n stable` 
          - 最新版`n latest`
          - 直接 n + 版本号 `12.13.0`
          - windows 升级 nodejs
            ```
            第一步：打开cmd查看之前的node版本安装的路径： where node
            第二步：直接去官网下载你想要的版本，安装在上述路径中覆盖即可。
            第三步： node -v 发现node版本更新到你想要的版本。
            ```
        * `npm install -g npm`
19. 变量提升
    ```js
    (function() {												
      var x=foo();					
      var foo;							
      var foo=function foo() {					
        var x = foo();							
        return 'foobar'					
        foo = function foo() {...}							
      };								
      return x;									
    })();												
    ```
    语句中变量的声明会提升，但是定义不会提升。以上代码等同于：  
    当执行到 x = foo() 时，由于foo未被定义为函数，所以会返回		  
    TypeError: foo is not a function					
20. 三等与二等
    ```js
    // 何时使用三等, 何时使用两等
    // 除了以下情况, 其他情况通通用 ===
    if (obj.a == null) {
      // 这里相当于 obj.a === null || obj.a === undefined 的简写
      // 表示obj的属性a是否存在
      // 这是jQuery源码中推荐的写法
    }
    ```
21.   JS中，0  NaN  ''  null  undefined  false  都表示false 
22.   JS执行顺序
      ```js
      ① 先查找所有的变量定义
      ② 再查找所有的函数声明
      ③ 当遇到函数调用时，则进入函数体 { } 中，
          先声明所有的函数变量、再进入参数列表、再从头到尾执行
      例子：
      console.log (a) // undefined
      var a = 100
      fn ('zhangsan') // 'zhangsan' 20
      function fn (name) {
       age = 20
       console.log (name,age)
       var age
      }
      ```
23. 内置对象9个  
    Object  Function  Array  String  Boolean  Number  Date  Error  Regexp 
24. this指向
    ```js
    var b = function(){		
        alert(this.a); 		
    },		
    obj = {		
        a:1,		
        b:b // 把函数独立出来 		
    };		
    var fun = obj.b;// 存储的是内存中的地址		
    fun();		
    答案：undefined		
    虽然fun是obj.b的一个引用，但是实际上，它引用的是b函数本身，
    因此此时的fun()其实 是一个不带任何修饰的函数调用，所以this指向window。		
    ```
25. 作用域
    ```js
    if(! "a" in window){
        var a = 1;
    }
    alert(a);
    输出undefined
    javascript只有函数作用域， 没有块作用域，所以在if 里面的赋值语句 
    var a = 1;
    分两步
    var a ; //这个声明语句会被提升到代码顶部。 
    a = 1;
    ```
26. 两等
    ```js
    undefined和null与任何其他有意义的值比较返回的都是false，但是null与undefined之间互相比较返回的是true。
    如：null == false 返回的就是false （因为存在null）
    但是 null == undefined 返回的就是 true （因为null是undefined的衍生）
    ```
27. forEach中的this指向
    ```js
    在使用vue时，获取组件的值时，常常用到 this
    this表示的是谁调用就指谁，
    但是注意在.forEach( function () {}) 中的 function中
    this不再指vue组件了
    ```
28. eval
    ```js
    eval() 函数可计算某个字符串，并执行其中的的 JavaScript 代码。
    如eval("2+3") , 则会执行"2+3"里的表达式，返回 5
    若eval(2+3)，则此时参数是不符合规定的，返回其中的参数，返回 2+3， 返回5
    ```
29. 一元运算符
    ```js
    // +new Array(017) 的输出是: NaN
    017表示 八进制，实际是 15
    new Array(15) 表示 创建一长度为 15 的数组
    // +new Array(15) 中的 + 表示一元运算符，
    // 即将new Array(15) 转化为数字，
    // 即相当于 Number(new Array(15))，
    // 即Number(",,,,,,,,,,,,,,") 
    ```
30. Promise的resolve只能接受一个参数, 多余的会省略
31. Number
    ```js
    Number(null)  // 返回 0
    以下 Number() 都是返回 0
    Number()
    Number(0)
    Number('')
    Number('0')
    Number(false)
    Number(null)
    Number([])
    Number([0])
    ```
32. 输出?
    ```js
    function test () {			  
      let n = 4399			      
      			                  
      function add () {			 
        n++			             
        console.log(n)			 
      }			
      			
      return {			
        n: n,			
        add: add,			
      }			
    }			

    // 问题：	result 与 result2 是两个相同的对象吗？
    return {
      n: n,
      add: add,
    }  // 其中，return.n 以及 return.add() 这两种方式调用的n是指向同一个的吗?? （应该不是）

    let result = test()			
    let result2 = test()			
    result.add()    			//  输出  4400
    result.add()			//  输出  4401  因为闭包可以①读取函数内部的变量;②使函数内变量的值保持在内存中
    console.log(result.n)			//  输出  4399  这里即使闭包add改变了变量n的值，result.n的值依然不变
    result2.add()			//  输出  4400
    输出： 4400 4401 4399 4400			//  result 与 result2 是两个不同的对象，因为它们有两个不同的作用域
    ```
33. arr
    ```js
    在前提：var arr =[1,3,2]下，下面哪个操作会返回一个数组，并且不是arr？
    arr.push(3)  // 返回新的长度
    arr.reverse() // 返回数组，但还是arr
    [].concat.call(arr,[])  // 正确  连接数组，且返回的是新数组
    [].sort.call(arr)  // 等价于 arr.sort() ，还是 返回 arr
    ```
34. 对象都是真值
    ```js
    JS中所有对象都是真值，所以 if (new Boolean(false)) console.log("true") // 输出 true
    在JS中, 只有 0  -0  NaN  ""  null  undefined  这6个值转布尔值时, 结果为false，其他时候都是 true
    注意new Boolean(0) 与 Boolean(0) 区别： 前者使用了new，会创建一个对象；后者只是显示类型转换，0转布尔值是false
    ```
35. JS转义
    ```js
    encodeURI
    encodeURIComponent
    decodeURI
    decodeURIComponent
    ```
36. 输出结果
    ```js
    var foo = {
      bar: function () {
        console.log(this) // Arguments
        return this.baz
      },
      baz: 1
    }
    ;(function () {
      return typeof arguments[0]();
    })(foo.bar)
    // undefined
    ```

    ```js
    var foo = {
      bar: function () {
        console.log(this) // 全局this
        return this.baz
      },
      baz: 1
    }
    ;(function (a) {
      return typeof a();
    })(foo.bar)
    // undefined
    ```
37. 快速取整
    ```js
    console.log(23.5|0) // 23
    console.log(-23.5|0) // -23
    ```
38. 让打印的JSON更容易阅读
    ```js
    console.log(JSON.stringify({ alpha: 'A', beta: 'B' }, null,"  "))
    ```
39. 日历
    ```js
    // 创建过去七天的数组
    [...Array(7).keys()].map(days => new Date(Date.now() - 86400000 * days));
    ```
40. 生成随机ID
    ```js
    // 生成长度为11的随机字母数字字符串
    Math.random().toString(36).substring(2);
    // hg7znok52x
    ```
41. 获取URL的查询参数
    ```js
    // 例子： ?foo=bar&baz=bing => {foo: bar, baz: bing}
    q={};
    location.search.replace(/([^?&=]+)=([^&]+)/g, (_, k, v) => q[k] = v);
    q;
    ```
42. 生成随机颜色
    ```js
    // 生成随机十六进制代码 如：'#c618b2'
    '#' + Math.floor(Math.random() * 0xffffff).toString(16).padEnd(6, '0');
    // 或者
    "#" + Math.random().toString(16).slice(-6)
    ```
43. 创建特定大小的数组
    ```js
    [...Array(3).keys()]  // [0, 1, 2]
    ```
44. 关于扩展运算符
    ```js
    function continuation(...funcs) {
      console.log(funcs)    // [["a1", "a2", "a3"]]
      console.log(funcs[0]) // ["a1", "a2", "a3"]
      console.log(funcs[1]) // undefined
      console.log(...funcs) // ["a1", "a2", "a3"]
    }
    let fn1 = "a1"
    let fn2 = "a2"
    let fn3 = "a3"
    let fn = [fn1, fn2, fn3]
    console.log(continuation(fn))
    ```
45. ES5时经常使用`var opts = opts || {}`表示默认值为空对象, ES6中因为`let`禁止`let opts = opts`同名传值  
    因此使用 `(opts = {}) => {}`表示默认值
46. 一般DOM节点的id用于`事件绑定(JS相关)`, class用于`样式修改(css相关)`
47. 用`this`方法时遇见的一个坑
    ```js
    class A {
    	constructor() {
    	  console.log(this) /// constructor 是构造函数,其中的this必然是当前class A {} 
    	  // this.say = this.say.bind(this)
    	}
    	say() {
    	  console.log(this)
    	}
    }
    
    new A().say() // A {}
    
    const fn = new A().say
    // console.log('fn: ', fn) 会发现fn就是一个函数,无被调用,因此 this 为 undefined
    fn() // undefined
    ```
48. 假如`Element`是一个类,那么可以简写  
    ```js
    let el = function () {
      return new Element(attr1, attr2, ...略)
    }
    el(attr1, attr2, ...略)
    ```
49. js获取某个字符的 Unicode 编码 值
    ```js
    '我'.charCodeAt(0) // 25105
    ```
50. `console.log('res:' + res);`  
    * 通常我们看不到想要的数据,因为往往看到的是`res: [object Object]`
    * `+` 加字符串,它会调用`toString()`
    * `toString()`
      - 除了null和undefined之外，其他的类型(数值、布尔、字符串、对象)都有toString()方法，它返回相应值的字符串表现(并不修改原变量)。
      - 每个对象都有一个toString()方法。
      - 当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。
      - 默认情况下，toString()方法被每个Object对象继承。如果此方法在自定义对象中未被覆盖，toString()返回 "[object type]"，其中type是对象的类型。
51. 内存泄漏  
    - 内存泄露会导致一系列问题，比如：运行缓慢，崩溃，高延迟
    - 内存泄露是指你用不到（访问不到）的变量，依然占居着内存空间，不能被再次利用起来
    - 使用`WeakMap`解决内存泄漏问题
      * 当使用Dom对象绑定事件时，Dom对象消失后若没有及时释放内存（置null），便会一直存在内存中。
      * 使用WeakMap保存Dom对象不会出现这样的问题，因为Dom对象消失后，JS的垃圾回收机制便会自动释放其所占用的内存。
      * 例子
        ```html
        <button type="button" id="btn">按钮</button>
        <script>
          let wm = new WeakMap();
          let btn = document.querySelector('#btn');
          wm.set(btn, {count: 0});
          btn.addEventListener('click', () => {
            let v = wm.get(btn);
            v.count++;
            console.log(wm.get(btn).count);
          });
        </script>
        ```
52. babel 工作原理  
    - 解析：将代码字符串解析成抽象语法树
    - 变换：对抽象语法树进行变换操作
    - 再建：根据变换后的抽象语法树再生成代码字符串
53. JS自定义事件
    ```js
    // 创建事件
    var event = document.createEvent('Event');   
    // 定义事件名为'build'.
    event.initEvent('build', true, true);
    // 监听事件
    elem.addEventListener('build', function (e) {
      // e.target matches elem
    }, false);
    // 触发对象可以是任何元素或其他事件目标
    elem.dispatchEvent(event);
    ```
54. 几道错题  
    ```js
    const shape = {
      radius: 10,
      diameter() {
        return this.radius * 2
      },
      perimeter: () => 2 * Math.PI * this.radius
    }
    
    shape.diameter()
    shape.perimeter()
    ```
    输出`20 and NaN`
    ```js
    class Chameleon {
      static colorChange(newColor) {
        this.newColor = newColor
        return this.newColor
      }
    
      constructor({ newColor = 'green' } = {}) { // 这里是什么意思
        this.newColor = newColor
      }
    }
    
    const freddie = new Chameleon({ newColor: 'purple' })
    freddie.colorChange('orange')
    ```
    TypeError:colorChange 是一个静态方法。静态方法被设计为只能被创建它们的构造器使用（也就是 Chameleon），并且不能传递给实例。因为 freddie 是一个实例，静态方法不能被实例使用，因此抛出了 TypeError 错误。
    ```js
    function getPersonInfo(one, two, three) {
      console.log(one)
      console.log(two)
      console.log(three)
    }
    
    const person = 'Lydia'
    const age = 21
    
    getPersonInfo`${person} is ${age} years old` // 奇怪的写法
    ```
    输出：`["", " is ", " years old"] "Lydia" 21` 如果使用标记模板字面量，第一个参数的值总是包含字符串的数组(按照非`${val}`进行裁剪)。其余的参数获取的是传递的表达式的值！(即`${val}`的值)
    ```js
    function checkAge(data) {
      if (data === { age: 18 }) {
        console.log('You are an adult!')
      } else if (data == { age: 18 }) {
        console.log('You are still an adult.')
      } else {
        console.log(`Hmm.. You don't have an age I guess`)
      }
    }
    
    checkAge({ age: 18 })
    ```
    输出：Hmm.. You don't have an age I guess // 引用类型不管是 == 还是 === 都是比较地址的
    ```js
    const obj = { a: 'one', b: 'two', a: 'three' }
    console.log(obj)
    ```
    输出`{ a: "three", b: "two" }` // 如果你有两个名称相同的键，则键会被替换掉。它仍然位于第一个键出现的位置，但是值是最后出现那个键的值。
    ```js
    String.prototype.giveLydiaPizza = () => {
      return 'Just give Lydia pizza already!'
    }
    
    const name = 'Lydia'
    name.giveLydiaPizza()
    ```
    输出`"Just give Lydia pizza already!"`
    ```js
    const a = {}
    const b = { key: 'b' }
    const c = { key: 'c' }
    
    a[b] = 123
    a[c] = 456
    
    console.log(a[b])

    对象的键被自动转换为字符串。我们试图将一个对象 b 设置为对象 a 的键，且相应的值为 123。
    然而，当字符串化一个对象时，它会变成 "[Object object]"。因此这里说的是，a["Object object"] = 123。然后，我们再一次做了同样的事情，c 是另外一个对象，这里也有隐式字符串化，于是，a["Object object"] = 456。
    然后，我们打印 a[b]，也就是 a["Object object"]。之前刚设置为 456，因此返回的是 456。
    ```
    输出`456` 
55. 匿名函数没有`constructor()`
    ```js
    let P = () => {
      this.a = 1
    }
    new P() // 错误，因为匿名函数没有 constructor()
    ```
56. 编程语言没有按引用传递,像数组、对象等其实也是按值传递,只是传递的是引用对象的地址的`副本`
    * [解析](https://www.cnblogs.com/thinkingthigh/p/7943086.html)
    * 对于数组和对象等是将对象(数组)的变量的值传递给了函数参数，这个变量保存的指向对象(数组)的地址。
    * 当函数改变这个地址指向的对象(数组)的内容时，同时也改变了函数外部变量指向的对象(数组)的内容；
    * 当函数改变的是变量的地址时，实际就与函数外部的变量失去了联系，变成了完全不同的对象了，不会对函数外部对象造成改变。
    ```js
    var change = function (nums1) {
      nums1 = [1] // [1]
    };

    var nums2 = [1, 2, 3, 0, 0, 0]
    change(nums2)
    console.log(nums2) //  [1, 2, 3, 0, 0, 0]
    ```
    