# 手动实现Proxy

## 实现
```js
/**代理实现类**/
function ProxyCopy(target,handle){
  /**浅拷贝工具方法**/
  this.clone=function(myObj){
    if(typeof(myObj) !== 'object' || myObj == null) return myObj;
    var newObj = new Object();
    for(let i in myObj){
      newObj[i] = this.clone(myObj[i]);
    }
    return newObj;
  }
  //深度克隆当前对象
  let targetCopy = this.clone(target);
  //遍历当前对象所有属性
  Object.keys(targetCopy).forEach(function(key){
    Object.defineProperty(targetCopy, key, {
      get: function() {
        return handle.get && handle.get(target,key);
      },
      /**
        * @param newVal 拦截的属性值
        */
      set: function(newVal) {
        handle.set && handle.set(target,key,newVal);
      }
    });
  });
  return targetCopy;
}

let person = {name:''};
let personCopy = new ProxyCopy(person,{
    get(target,key){
        console.log('get方法被拦截。。。 实现原理为通过属性的getter驱动函数调用该方法');
        return target[key];
    },
    set(target,key,value){
        console.log('set方法被拦截。。。实现原理为通过属性的setter驱动函数调用该方法');
        target[key]=value+"www";
    }
});

personCopy.name = 'arvin';  // set方法被拦截。。。
console.log(personCopy.name);   // get方法被拦截。。。
```

## 参考
- [JS原生-es6之proxy的实现](https://blog.csdn.net/qq_38603437/article/details/92855683)
