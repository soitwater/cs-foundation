# Render & Slot
## 注意
- 易错：Render函数渲染子节点[]时，VNode 仅能使用一次，不能重复使用，  
  如错误写法: `let a = creatElement(...); render (h) {return h ("p", {}, [a, a])}`  
  同理, `render (h) {return h ("p", {}, [this.$slots.default, this.$slots.default])}`，也是错误的  
  注: v2.5.18 开始 vue 支持 vnode 复用了.

## 例子（Render）
test1.vue
```html
<template>
  <div id="test1">
    <render-temple
      v-for="(item, index) of tdATA" :key="index"
      :type="item.type" :name="item.name">
    </render-temple> 
  </div>
</template>

<script>
import RenderTemple from "./renderTemple"
export default {
  data () {
    return {
      tdATA: [{type:"小说",name:"西游记"},{type:"诗词",name:"唐诗三百首"}],
    }
  },
  components: {
    RenderTemple
  },
}
</script>
```
renderTemple.js
```js
export default {
  render : function (h) { 
    return h ( 
      "p",
      { 
        domProps: {
          innerHTML:this.type + "->" + this.name,
        }
      },
    ) 
  }, 
  props: {
    type: { 
      type: String, 
      required: true 
    },
    name: { 
      type: String, 
      required: true 
    },
  }  
};
```

## 例子（渲染多个 VNode）
test1.vue 同 例子（Render）  
renderTemple.js
```js
let childP = {
  render (h) {
    return h ("div", "text") // “text” 即 “domProps”
  }
}
export default {
  render : function (h) { 
    return h ( 
      "div",
      { 
        // domProps: { // 声明 donProps > innerHTML 将覆盖子元素
        //   innerHTML:this.type + "->" + this.name,
        // }
      },
      [h(childP), h(childP)]
    ) 
  }, 
  props: {
    type: { 
      type: String, 
      required: true 
    },
    name: { 
      type: String, 
      required: true 
    },
  }  
};
```

## 例子（slot的深复制）
test1.vue 同 例子(Render 与 Slot 混合)
renderTemple.js:
```js
export default {
  render : function (h) {
    function cloneVNode (vnode) { // 克隆 slot 节点的方法
      // 递归遍历子节点，并克隆
      const clonedChildren = vnode.children && vnode.children.map(vnode => cloneVNode(vnode))

      const cloned = h (vnode.tag, vnode.data, clonedChildren)
      cloned.text = vnode.text
      cloned.isComment = vnode.isComment
      cloned.componentOptions = vnode.componentOptions
      cloned.elm = vnode.elm
      cloned.context = vnode.context
      cloned.ns = vnode.ns
      cloned.isStatic = vnode.isStatic
      cloned.key = vnode.key

      return cloned
    } 
    const vNodes = this.$slots.default
    const clonedVNodes = vNodes.map(vnode => cloneVNode(vnode))

    return h ( 
      "div",
      clonedVNodes
    ) 
  }, 
};
```

## 例子(Render 与 Slot 混合)
test1.vue
```html
<template>
  <div id="test1">
    <render-temple :level="4"> 
      <p slot="myslot">render function</p> 
    </render-temple> 
  </div>
</template>

<script>
import RenderTemple from "./renderTemple"

export default {
  data () {
    return {
    }
  },
  methods: {
  },
  components: {
    RenderTemple
  },
}
</script>
```
renderTemple.js
```js
export default {
  render : function (createElement) { // 支持简写 h
    return createElement( 
      'h'+this.level, // 标签
      { // 数据对象
        'class':{ 
          show:true, 
          hide:false, 
        }, 
        style:{ 
          background:'red', 
        }, 
        attrs:{ 
          name:'h-ex', 
          id:'h-id'
        }, 
        props:{ 
          myprops:true, 
        }, 
        on: { 
          click: function(event){ 
            console.log(event, this) 
          } 
        }, 
        nativeOn:{ 
          click:function(event) { 
            alert("nativeOn") 
          } 
        } 
      }, 
      [ // 子元素
        this.$slots.myslot, // this.$slots 具体指 调用者(test1.vue)里的所有slots，this.$slots.myslot 指slots中的具名为“myslot”的slots，
        createElement('p', { 
          domProps:{ 
            innerHTML:'holle render'
          } 
        }) 
      ] 
    
      ) 
  }, 
  props: { 
    level: { 
      type: Number, 
      required: true 
    } 
  }  
};
```
