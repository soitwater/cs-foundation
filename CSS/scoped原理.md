# scoped原理

## 原理
> - 在vue组件中我们经常需要给style添加scoped来使得当前样式只作用于当前组件的节点。  
> - 添加scoped之后，实际上vue在背后做的工作是将当前组件的节点添加一个像”data-v-1233”这样唯一属性的标识，  
> - 当然也会给当前style的所有样式添加`[data-v-1233]`这样的话，就可以使得当前样式只作用于当前组件的节点。
> - 对组件样式的重载，最好在App.vue中声明，因为App.vue的style没有用scoped

## 参考
- [修改elementUI组件样式无效的问题研究](https://www.cnblogs.com/goloving/p/9119214.html)