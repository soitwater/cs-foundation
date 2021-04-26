# js原型的本质就只是链表

## 解释
- 一般的链表结构
  ```js
  function Node(val = null, next = null) {
    this.val = val;
    this.next = next;
  }
  ```
- 这就是前端那些`原型`,`原型链`,`构造函数`,`this`,`箭头函数`,`bind`,`call`,`apply`的本质
  * 原型链：复杂的链表
  * 原型：链表中的节点
  * this：链表中当前指向的那一个原型
  * bind,call,apply：用来绑定和指向不同的原型