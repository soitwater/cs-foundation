# 实现VirtualDOM与diff

## 概念
- 与 Dom 操作相比，VDom 使用 JS 进行操作，性能更佳
- 举例：在一个事件队列中，需要更新10个DOM元素。
  * 浏览器在收到第一个DOM请求后不知道还有9次更新请求，因此将迭代10次；
  * 使用 VDom 本质是利用JS对象（VNode）描述DOM结构
    1. 新创建一个 VNode，10次更新 DOM 操作实质为更新该 VNode（有2个 VNode）
    2. 将更新后的 VNode 与缓存的 VNode（更新前的 VNode）进行对比
    3. 更新 DOM 结构

## 为什么传统的树节点比较算法的时间复杂度是O(n^3),而react的diff算法只需要O(n)
- 传统的树比较算法需要很多完备的考虑,而前端框架的DIFF算法则是通过`一系列的前提假设`,将O(n^3)降低到O(n)
- 假设 / 前提
  * DOM跨层级的移动操作很少
  * 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
  * 对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。

## 前置知识
- 一个DOM节点本身也是一个非常复杂的对象，因为该DOM对象包含众多的`Attributes`
  ```js
  let div = document.createElement("div")
  for (let key in div) {
    console.log(`${key} >>> ${div[key]}`)
  }
  // 可以看到一个DIV节点便包含N多的键值对，更不必说一个页面上百个节点，在发生回流的时候会多么影响性能
  ```
 - 可以使用JS对象模拟DOM对象，且该JS对象上仅有我们关注的Attribute，不含更多的多余属性，这样编辑时性能更佳

## VirtualDOM
手动实现VDOM,例子如下  
### express创建的项目 - app.js
```js
var express = require('express');											
var fs = require('fs');											
var path = require('path');											
var bodyParser = require('body-parser');	
const cookieParase = require('cookie-parser');

var app = express();	
app.use(cookieParase())	// 解析cookie									
app.use(bodyParser.json());											
app.use(bodyParser.urlencoded({ extended: false }));		
// 跨域
app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept")
  next()
})
// 访问静态资源											
app.use(express.static(path.resolve(__dirname, './dist')));				
// 访问更多页面
app.get('/vdom', function (req, res) {											
  var html = fs.readFileSync(path.resolve(__dirname, 'dist/vdom/index.html'), 'utf-8');					
  res.send(html);											
});		
// 监听											
app.listen(8080, function () {											
  // console.log('success listen...');
});											
```
### dist/vdom/index.js
```js
/* eslint-disable */
import { createElement, render, renderDom } from './element.js';
import diff from './diff.js';
import patch from './patch.js';

let virtualDom = createElement('ul', { class: 'list' }, [ // Element 类的实例
  createElement('li', { class: 'item' }, ['周杰伦']),
  createElement('li', { class: 'item' }, ['林俊杰']),
  createElement('li', { class: 'item' }, ['王力宏'])
]);
let el = render(virtualDom); // 渲染虚拟DOM得到 --> 真实的DOM结构
renderDom(el, document.getElementById('root')) // 直接将DOM添加到页面内

let virtualDom2 = createElement('ul', { class: 'list-group' }, [
  createElement('li', { class: 'item active' }, ['七里香']),
  createElement('li', { class: 'item' }, ['一千年以后']),
  createElement('li', { class: 'item' }, ['需要人陪'])
]);

// diff一下两个不同的虚拟DOM
let patches = diff(virtualDom, virtualDom2);
console.log('patches --> \n', patches)
// 将变化打补丁，更新到el
patch(el, patches);
```
### dist/vdom/index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
</head>
<body>
  <div id="root"></div>
  <script src="./index.js" type="module"></script>
</body>
</html>
```
### dist/vdom/diff.js
```js
// diff.js

// 对比两个对 Element实例 的差异

// diff 的对比过程
// 新节点不存在
// 旧/新节点都为文本节点。但文本不同
// 标签相同。但属性有差异 --> 递归子节点
// 标签不同
// 把当前元素的补丁放入`patches`

function diff(oldTree, newTree) {
  // 声明变量patches用来存放补丁的对象
  let patches = {};
  // 第一次比较应该是树的第0个索引
  let index = 0;
  // 递归树 比较后的结果放到补丁里
  walk(oldTree, newTree, index, patches);
  return patches;
}

function walk(oldNode, newNode, index, patches) {
  // 每个元素都有一个补丁
  let current = [];

  if (!newNode) { // rule1
    current.push({ type: 'REMOVE', index });
  } else if (isString(oldNode) && isString(newNode)) {
    // 判断文本是否一致
    if (oldNode !== newNode) {
      current.push({ type: 'TEXT', text: newNode });
    }

  } else if (oldNode.type === newNode.type) {
    // 比较属性是否有更改
    let attr = diffAttr(oldNode.props, newNode.props);
    if (Object.keys(attr).length > 0) {
      current.push({ type: 'ATTR', attr });
    }
    // 如果有子节点，遍历子节点
    diffChildren(oldNode.children, newNode.children, patches);
  } else {    // 说明节点被替换了
    current.push({ type: 'REPLACE', newNode });
  }

  // 当前元素确实有补丁存在
  if (current.length) {
    // 将元素和补丁对应起来，放到大补丁包中
    patches[index] = current;
  }
}

function isString(obj) {
  return typeof obj === 'string';
}

function diffAttr(oldAttrs, newAttrs) {
  let patch = {};
  // 判断老的属性中和新的属性的关系
  for (let key in oldAttrs) {
    if (oldAttrs[key] !== newAttrs[key]) {
      patch[key] = newAttrs[key]; // 有可能还是undefined
    }
  }

  for (let key in newAttrs) {
    // 老节点没有新节点的属性
    if (!oldAttrs.hasOwnProperty(key)) {
      patch[key] = newAttrs[key];
    }
  }
  return patch;
}

// 所有都基于一个序号来实现
let num = 0;

function diffChildren(oldChildren, newChildren, patches) {
  // 比较老的第一个和新的第一个
  oldChildren.forEach((child, index) => {
    walk(child, newChildren[index], ++num, patches);
  });
}

// 默认导出
export default diff;
```
### dist/vdom/patch.js
```js
// patch.js
// 更新某个元素(打上补丁)

import { Element, render, setAttr } from './element.js';

let allPatches;
let index = 0;  // 默认哪个需要打补丁

function patch(node, patches) {
  allPatches = patches;

  // 给某个元素打补丁
  walk(node);
}

function walk(node) {
  let current = allPatches[index++];
  let childNodes = node.childNodes;

  // 先序深度，继续遍历递归子节点
  childNodes.forEach(child => walk(child));

  if (current) {
    doPatch(node, current); // 打上补丁
  }
}

function doPatch(node, patches) {
  // 遍历所有打过的补丁
  patches.forEach(patch => {
    switch (patch.type) {
      case 'ATTR':
        for (let key in patch.attr) {
          let value = patch.attr[key];
          if (value) {
            setAttr(node, key, value);
          } else {
            node.removeAttribute(key);
          }
        }
        break;
      case 'TEXT':
        node.textContent = patch.text;
        break;
      case 'REPLACE':
        var newNode = patch.newNode;
        newNode = (newNode instanceof Element) ? render(newNode) : document.createTextNode(newNode);
        node.parentNode.replaceChild(newNode, node);
        break;
      case 'REMOVE':
        node.parentNode.removeChild(node);
        break;
      default:
        break;
    }
  });
}

export default patch;
```
### dist/vdom/element.js
```js
/* eslint-disable */

// element.js

// 虚拟DOM元素的类，构建实例对象，用来描述DOM
class Element {
  constructor(type, props, children) {
    this.type = type; // 标签
    this.props = props; // 元素属性
    this.children = children; // 子节点(两类:元素,或者文本节点)
  }
}
// 创建虚拟DOM，返回虚拟节点(object)
function createElement(type, props, children) {
  return new Element(type, props, children);
}

// render方法可以将虚拟DOM转化成真实DOM
function render(domObj) { // domObj 是 Element 的实例
  // 根据type类型来创建对应的元素
  let el = document.createElement(domObj.type);

  // 再去遍历props属性对象，然后给创建的元素el设置属性
  for (let key in domObj.props) {
    // 设置属性的方法
    setAttr(el, key, domObj.props[key]);
  }

  // 遍历子节点
  // 如果是虚拟DOM，就继续递归渲染
  // 不是就代表是文本节点，直接创建
  domObj.children.forEach(child => {
    child = (child instanceof Element) ? render(child) : document.createTextNode(child);
    // 添加到对应元素内
    el.appendChild(child);
  });
  return el;
}

// 设置属性
function setAttr(node, key, value) {
  switch (key) { // 有两个属性比较特殊...
    case 'value':
      // node是一个input或者textarea就直接设置其value即可
      if (node.tagName.toLowerCase() === 'input' ||
        node.tagName.toLowerCase() === 'textarea') {
        node.value = value;
      } else {
        node.setAttribute(key, value);
      }
      break;
    case 'style':
      // 直接赋值行内样式
      node.style.cssText = value;
      break;
    default:
      node.setAttribute(key, value);
      break;
  }
}

// 将元素插入到页面内
function renderDom(el, target) {
  target.appendChild(el);
}

export {
  Element,
  createElement,
  render,
  setAttr,
  renderDom
}
```

## diff
- VDOM算法的核心是diff算法，即比较两棵DOM树的差异  
- 两个树的完全的 diff 算法是一个时间复杂度为 O(n^3) 的问题(跨层级)
- 但是在前端当中，你很少会跨越层级地移动DOM元素。所以 VDOM 只会对同一个层级的元素进行对比,复杂度为O(n)
- diff算法包含两种情况：①同层级元素的比较(O(n))
  * 整个元素都不一样，即元素被replace掉
  * 元素的attrs不一样
  * 元素的text文本不一样
  * 元素顺序被替换，即元素需要reorder


## 参考
- [深度剖析：如何实现一个 Virtual DOM 算法](https://www.cnblogs.com/fengyuqing/p/virtual-dom.html)
- [面试官：你了解 vue 的diff算法吗？](https://juejin.im/post/5ad6182df265da23906c8627)
- [Github上优秀源码参考](https://github.com/livoras/simple-virtual-dom/tree/master/lib)
- [解析vue2.0的diff算法](https://segmentfault.com/a/1190000008782928)
- [合格前端系列第五弹-Virtual Dom && Diff](https://zhuanlan.zhihu.com/p/27437595)
- [虚拟 DOM 内部是如何工作的](http://www.zcfy.cc/article/the-inner-workings-of-virtual-dom-rajaraodv-medium-3248.html)
- [让虚拟DOM和DOM-diff不再成为你的绊脚石](https://juejin.im/post/5c8e5e4951882545c109ae9c)