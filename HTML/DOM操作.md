# DOM操作

## 查找
语法|说明
-|-
document.getElementById("id")|-
document/element.getElementsByClassName("class")|-
document/element.getElementsByTagName("tag")|-
document.getElementsByName("name")|-
document/element.querySelector("选择器")|-
document/element.querySelectorAll("选择器")|-
document.documentElement|获取页面中的HTML标签
document.body|获取页面中的body标签
document.all|获取页面中的所有节点

## 创建
语法|说明
-|-
document.createElement("tag名")|-
document.createAttribute("attr名")|-
document.createTextNode("文本内容")|与 elment.innerHTML 作用一致
document.createComment("注释内容")|创建注释节点
document.createDocumentFragment()|创建文档片段节点

## 插入
语法|说明
-|-
parent.appendChild(子节点)|向父节点的最后一个子节点后追加新节点
parent.insertBefore(newChild, existingChild)|向父节点的某个特定子节点之前插入新节点
element.setAttributeNode(属性节点)|为元素添加属性节点(不能设置值)
element.setAttribute(属性名, 属性值)|为元素增加指定属性节点, 并设定属性值

## 删除
语法|说明
-|-
parent.removeChild(子元素)|删除子节点, 返回值为子节点
element.removeAttribute(属性名)|删除具有指定属性名称的属性，无返回值
element.removeAttributeNode(属性节点)|删除指定属性，返回值为删除的属性 

## 修改
语法|说明
-|-
parent.replaceChild(newChild, existingChild)|用新节点代替目标节点的子节点

## Node对象

Node对象属性|说明
-|-
firstChild|第一个子节点
lastChild|最后一个子节点
previousSibling|前一个兄弟节点
nextSibling|后一个兄弟节点
ownerDocument|返回整个文档
parentNode|父节点
nodeName|节点名称
nodeType|节点类型(数字)
nodeValue|以文本格式获取或设置节点的值

<br/>

Node对象方法|说明
-|-
appendChild(newChild)|略
cloneNode(cloneChild)|cloneChild 是一个布尔值,表示是否克隆其所有子节点
hasChildNode()|是否有子节点
insertBefore(newChild, referenceNode)|在 referenceNode 前插入 newChild
removeChild(childNode)|删除并返回该子节点

## 类名操作
- `为 <div> 元素添加 class:`
  document.getElementById("myDIV").classList.add("mystyle");
- `为 <div> 元素添加多个类:`
  document.getElementById("myDIV").classList.add("mystyle", "anotherClass", "thirdClass");
- `为 <div> 元素移除一个类:`
  document.getElementById("myDIV").classList.remove("mystyle");
- `为 <div> 元素移除多个类:`
  document.getElementById("myDIV").classList.remove("mystyle", "anotherClass", "thirdClass");
- `检查是否含有某个CSS类`
  myDiv.classList.contains('myCssClass'); //return true or false

## 其他
- 获取某元素的最后一个子元素
  ```js
  let a = document.getElementsByClassName("content-container")[0]
  console.log(a.lastElementChild)
  ```
- 清空子元素
  `element.innerHTML = ""`

## 参考
- [DOM常用操作](https://www.cnblogs.com/yinshuige/p/5812095.html)