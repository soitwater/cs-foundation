# LRU_Cache
- 根据数据的历史访问记录来进行淘汰数据，其核心思想是`'如果数据最近被访问过，那么将来被访问的几率也更高'`
- 算法实现
  * 新数据插入到链表头部；
  * 每当缓存命中（即缓存数据被访问），则将数据移到链表头部；
  * 当链表满的时候，将链表尾部的数据丢弃

## 实现
```js
function Node(value, next) {  
  this.value = value;  
  this.next = next;  
}  

function LRUCache(initialCapacity, initialValue) {  
  this.size = 0;  
  this.head = null;  
  this.tail = null;  
  this.capacity = initialCapacity;  

  if (initialValue) {  
    var key = Object.keys(initialValue)[0];
    //Object.keys()方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 for...in 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）。  
    var value = initialValue[key];  
    this.cache(key, value);  
  }  
}  

//是否为空  
LRUCache.prototype.isEmpty = function() {  
  return this.head == null && this.tail == null;  
};  

//在头部做添加  
LRUCache.prototype.addFirst = function(obj) {  
  var newNode = new Node(obj, null);  
  if (this.isEmpty()) {  
    this.head = this.tail = newNode;  
  } else {  
    this.head.next = newNode;  
    this.head = newNode;
    //链表是反着的，head是最后一个节点  
  }  
  this.size++;  
  newNode = null;  
};  

//在尾部做删除  
LRUCache.prototype.removeLast = function() {  
  if (!this.isEmpty()) {  
    var key = Object.keys(this.tail.value)[0];  
    delete this[key];  
    this.tail = this.tail.next;  
    if (this.tail == null) {  
      this.head = null;  
    }  
    this.size--;  
  }  
};  

//将节点向头部移动  
LRUCache.prototype.moveForward = function(key) {  
  var node = this.search(key);  
  if (node && node.next) {  
    var next = node.next;  
    var temp = node.value;  
    node.value = next.value;  
    next.value = temp;
    //位置交换了一下  
  }  
};  

//缓存元素  
LRUCache.prototype.cache = function(key, value) {  
  //先查找是否已存在  
  var result = this.search(key);  
  //已存在采取覆盖value策略  
  if (result) {  
      result.value[key] = value;  
      this[key] = value;  
  } else {  
    var obj = {};  
    obj[key] = value;  
    this.addFirst(obj);  
    var that = this;  
    Object.defineProperty(this, key, {  
      set : function(x) {  
        that.moveForward(key);  
        value = x;  
      },  
      get : function() {  
        that.moveForward(key);  
        return value;  
      },  
      configurable : true,  
      enumerable : true  
    });  
    //超出容量的话，将最少使用的删除  
    if (this.size > this.capacity) {  
      this.removeLast();  
    }  
  }  
  //可以链式调用  
  return this;  
};  

//删除元素  
LRUCache.prototype.del = function(key) {  
  if (this.search(key)) {  
    var previous = null;  
    var cur = this.tail;  
    for (; cur; cur = cur.next) {  
      if (cur.value.hasOwnProperty(key)) {  
        break;  
      }  
      previous = cur;  
    }  
    if (previous) {  
      if (previous.next) {
        //待定，感觉有点问题，删除的是目标值的后一个节点  
        previous.next = previous.next.next;  
        this.size--;  
      }  
    } else {  
      this.tail = this.tail.next;  
      this.size--;  
    }  
  }  
  if (this.hasOwnProperty(key)) {  
    return (delete this[key]);  
  } else {  
    return (delete LRUCache.prototype[key]);  
  }  
};  

//查找元素  
LRUCache.prototype.search = function(key) {  
  for (var e = this.tail; e; e = e.next) {  
    if (key in e.value) {  
      return e;  
    }  
  }  
  return null;  
}
```

## 参考
- [LRU缓存的js实现](https://blog.csdn.net/haley_guo/article/details/80283277)