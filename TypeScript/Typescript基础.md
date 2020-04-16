# Typescript基础

## 基础
### 数据类型
- 原始数据类型包括：布尔值、数值、字符串、null、undefined 以及 ES6 中的新类型 Symbol
- 对象类型
  ```js
  // 新建的对象的属性必须为 interface 的子集
  interface IPerson {
    name: string;
    age?: number;
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