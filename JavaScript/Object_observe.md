# Object_observe

- 每当读取属性 or 修改属性，都将执行回调函数
- 该API已被废弃,但可被原生JS实现`Proxy （代理）`和`Object.defineProperty 实现`
- Proxy 不能监听源对象，只能监控代理对象
- Object.defineProperty 有新增属性的时候，无法做到自动监听

## Proxy
不能监听源对象，只能监控代理对象
```js
class Obverse {
  constructor(target, callback) {
    this.target = target
    this.callback = callback
    this.index = Obverse.targets.indexOf(target)
    this.index === -1 ? this.obverseNewTarget() : 1// 监听新对象前的准备
    this.obverseByProxy() // 监听对象上的所有属性
    return Obverse.proxy[this.index]
  }

  obverseNewTarget() {
    this.index = Obverse.targets.length
    Obverse.targets.push(this.target) // 堆内存：监听的是 Obverse.targets
    Obverse.callbacks.push([]) // 监听同一对象时,允许多个回调函数,因此这里是数组
  }
  obverseByProxy() {
    let _this = this // 注意下面的Object.defineProperty里, this将为Object,因此使用_this
    let callbackList = Obverse.callbacks[_this.index]
    callbackList.push(_this.callback)
    Obverse.proxy[_this.index] = Obverse.proxy[_this.index] || new Proxy(_this.target, {
      set(target, attr, value) {
        target[attr] = value
        for (var i = 0; i < callbackList.length; i++) {
          callbackList[i].call(target)
        }
      }
    })
  }
}
Obverse.targets = Obverse.targets || [] // 由被监听对象组成的数组
Obverse.callbacks = Obverse.callbacks || [] // 所有的回调函数, 其元素也是数组
Obverse.proxy = []

// test
let person = {
  name: "xxx",
  age: 10
}
let person2 = {
  name: "yyy",
  age: 11
}
let obverse = new Obverse(person, function () { // 不要使用箭头函数(丢失 this 指向)
  console.log(`set: ${this.name}, ${this.age}`)
})
let obverse2 = new Obverse(person2, function () { // 不要使用箭头函数(丢失 this 指向)
  console.log(`set2: ${this.name}, ${this.age}`)
})
obverse.name = "xxx1"
obverse2.name = "yyy1"
```

## Object.defineProperty
有新增属性的时候，无法做到自动监听
```js
class Obverse {
  constructor(target, callback) {
    this.target = target
    this.callback = callback
    this.index = Obverse.targets.indexOf(target)
    if (this.index === -1) {
      this.obverseNewTarget() // 监听新对象前的准备
    }
    this.obverseAllAttributeAtTarget() // 监听对象上的所有属性
  }

  obverseNewTarget() {
    this.index = Obverse.targets.length
    Obverse.targets.push(this.target) // 堆内存：监听的是 Obverse.targets
    Obverse._targets.push(Object.assign({}, this.target)) // 但其实 get set 的都是 Obverse._targets
    Obverse.callbacks.push([]) // 监听同一对象时,允许多个回调函数,因此这里是数组
  }
  obverseAllAttributeAtTarget() { // 对target上的每一个attribute实行监听
    let _this = this
    let callbackList = Obverse.callbacks[_this.index]
    callbackList.push(_this.callback)
    for (let attr in this.target) {
      Object.defineProperty(this.target, attr, {
        set (newVal) {
          Obverse._targets[_this.index][attr] = newVal
          for (let f of callbackList) {
            f.call(_this.target)
          }
        },
        get() {
          return Obverse._targets[_this.index][attr]
        }
      })
    }
  }
}
Obverse.targets = Obverse.targets || [] // 由被监听对象组成的数组
Obverse._targets = Obverse._targets || [] // 实际 get set 的由被监听对象组成的数组
Obverse.callbacks = Obverse.callbacks || [] // 所有的回调函数, 其元素也是数组

// test
let person = {
  name: "xxx",
  age: 10
}
let obverse = new Obverse(person, function () { // 不要使用箭头函数(丢失 this 指向)
  console.log(`set: ${this.name}, ${this.age}`)
})
obverse = new Obverse(person, function() {
  console.log(`set: second callback`)
})
person.name = "yyy"
person.age = 20
```

## 参考
- [使用原生 js 模拟被废弃的 Object.observe()](https://www.jianshu.com/p/32b853df8b99)
