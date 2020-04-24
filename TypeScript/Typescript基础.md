# Typescript基础

## 基础
### 数据类型
- 原始数据类型包括：布尔值、数值、字符串、null、undefined 以及 ES6 中的新类型 Symbol
- 对象类型
  ```js
  // 新建的对象的属性必须为 interface 的子集
  interface IPerson {
    name: string;
    age?: number; // 问号表示`可选`
  }
  let tom: IPerson = {
    name: 'Tom',
  }

  // 允许任意属性,且其值类型任意(注意：字段 name 的类型必须是 [propName: string] 类型的子集) 
  interface Person {
    name: string;
    [propName: string]: any;
  }

  // 只读属性 readonly
  interface Person {
    readonly id: number;
  }
  ```
- 数组类型
  ```js
  let fibonacci: number[] = [1, 1, 2, 3, 5];
  // 数组泛型
  let fibonacci: Array<number> = [1, 1, 2, 3, 5];
  // 类数组，如 arguments
  function sum() {
    let args: {
      [index: number]: number; // 约束当索引的类型是数字，且值的类型必须是数字
      length: number;
      callee: Function;
    } = arguments;
  }
  ```
- 联合类型：`let myFavoriteNumber: string | number;`
- `:void`用于表示函数无返回值
- `:any`用于表示任意类型
- 类型断言：`cat as Animal`, 让对象`cat`(`Cat类`)断言为`Animal类`
  * (任何类型都可被断言为`any`)
  * 类型断言不是类型转换

### 声明
- 声明通常放一个文件中，文件名以`.d.ts`结尾
- 声明用于将一个JS库改造成TS的库,如` @types/node`其实是`nodejs`的声明文件
- `declare var jQuery: (selector: string) => any;`：全局声明函数`jQuery`

### 内置对象
- 如：Boolean、Error、Date、RegExp
  ```js
  let e: Error = new Error('Error occurred');
  ```
- 如：Document、HTMLElement、Event、NodeList
  ```js
  let body: HTMLElement = document.body;
  let allDiv: NodeList = document.querySelectorAll('div');
  document.addEventListener('click', function(e: MouseEvent) {});
  ```

## 进阶
### 类型别名
- `type Name = string;`用`Name`代替`string`类型
### 字符串字面量类型
- `type EventNames = 'click' | 'scroll' | 'mousemove';`
  * 定义类型`EventNames`,它的值只能是`'click' | 'scroll' | 'mousemove'`三者之一
### 元组
- 数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象
- `let tom: [string, number] = ['Tom', 25];`
- `let tom: [string, number] = ['Tom'];`这是错误的,因为`[string, number]`明确了需要该元组需两个元素
### 枚举
- 例子
  ```js
  enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
  console.log(Days["Sun"] === 0); // true; 枚举成员会被赋值为从 0 开始递增的数字
  console.log(Days[0] === "Sun"); // true

  enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};
  console.log(Days["Mon"] === 1); // true
  console.log(Days["Tue"] === 2); // true; 增加步长为1
  ```
### 类
- 例子
  ```js
  class Cat extends Animal { // 继承
    constructor(name) {
      super(name); // 调用父类的 constructor(name)
      console.log(this.name);
    }
    sayHi() {
      return 'Meow, ' + super.sayHi(); // 调用父类的 sayHi()
    }
    get name() { // 存取器
      return 'Jack';
    }
    set name(value) {
      console.log('setter: ' + value);
    }
    name = 'Jack'; // this.name = 'Jack' 的简化
    static num = 42; // 静态属性
    static isAnimal(a) { // 静态方法
      return a instanceof Animal;
    }
  }
  ```
- 修饰符`public、private 和 protected`
- 只读属性`public readonly name = 'Jack';`
- 抽象类
  ```js
  abstract class Animal {
    public name;
    public constructor(name) {
      this.name = name;
    }
    public abstract sayHi();
  }
  ```

### 接口
- 接口继承接口
  ```js
  interface Alarm {
    alert(): void;
  }

  interface LightableAlarm extends Alarm {
    lightOn(): void;
    lightOff(): void;
  }
  ```
- 一个类可继承多个接口
  ```js
  interface Alarm {
    alert(): void;
  }

  interface Light {
    lightOn(): void;
    lightOff(): void;
  }

  class Car implements Alarm, Light {
    alert() {
      console.log('Car alert');
    }
    lightOn() {
      console.log('Car light on');
    }
    lightOff() {
      console.log('Car light off');
    }
  }
  ```

### 泛型
- 泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性
- 例子
  ```js
  function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
      result[i] = value;
    }
    return result;
  }
  createArray<string>(3, 'x');

  // 多个类型参数
  function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]]; // 交换元组
  }
  swap([7, 'seven']); 
  ```
- 泛型约束
  * 假如传入的变量，其类型不含`length`属性，将导致报错。解决方法是对传入的变量类型进行约束
  * 例子
    ```js
    interface Lengthwise {
      length: number;
    }

    function loggingIdentity<T extends Lengthwise>(arg: T): T {
      console.log(arg.length);
      return arg;
    }
    ```
- 泛型接口
  ```js
  interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
  }

  let createArray: CreateArrayFunc<any>;
  createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
      result[i] = value;
    }
    return result;
  }

  createArray(3, 'x'); // ['x', 'x', 'x']
  ```
- 泛型类
  ```js
  class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
  }

  let myGenericNumber = new GenericNumber<number>();
  myGenericNumber.zeroValue = 0;
  myGenericNumber.add = function(x, y) { return x + y; };
  ```

## 其他
### is 关键字
- 用于判断类型时，将待测变量的类型限定为某一种或某几种类型
  * 例子
    ```ts
    // test is string 若test是string类型，将待测变量的类型限定为 string
    function isString(test: any): test is string{ 
      return typeof test === “string”;
    }
    function example(foo: any){
      if(isString(foo)){
        console.log(“it is a string” + foo);
        console.log(foo.length); // 正确，因为已经
        // 编译错误，因为`.toExponential(2)`是number类型的方法
        // 除非改成 `function isString(test: any): boolean {`
        console.log(foo.toExponential(2)); 
      }
    }
    example(“hello world”);
    ```