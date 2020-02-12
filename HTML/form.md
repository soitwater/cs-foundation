# form

```html
访问 <form/>  除了寻常的document.getElementById() 还有 document.myFormId
获取页面<form/>集合(返回一个数组，因此有legth属性)  document.forms
```

- 表单的功能通过`<input type="" /> `实现
  * 分类：
    - ①文本输入类型：text  password  textarea
    - ②点选类型  checkbox  radio
    - ③选择类型  下拉菜单`<select><option></option></select>`
                上下滚动菜单 `<select size="4"><option></option></select>`
    - ④按钮类型  button  submit  reset
    - ⑤其他类型：hidden(不可见的表单)

- `<input/>`共有属性：
  * name
  * value
  * form 返回当前元素的form对象
  * type
  * focus() 获得焦点 ；blur() 失去焦点 ；   
    // 调用focus() 会 触发 onfocus处理事件, onblur() 会触发 onblur处理程序；  
    但是，  * submit() 方法不会触发submit 处理程序

```js
// 按钮元素
<input type="button" name="btn" value="确定"/>  其value属性表示按钮上的文本

// 文本元素
<input type="text" value="文本框中的文本" size="文本框的字符宽度" maxlength="确定用户在文本框中可以输入的最大字符数"/>  
另外还有 readonly="readonly" 防止修改文本框的内容
另外包含事件：focus, blur, change(当文本框失去焦点时的值不同于它获得焦点时的值, 才触发该事件), select(用户选择文本框中的文件时触发), keydown, keypress, keyup,

// textarea元素
<textarea/> 元素类似于 <input type="textarea"/>，但<textarea/> 有rows属性，cols属性，
但<textarea/>没有value属性，其文本写在<textarea>文本</textarea> （但 textarea 对象是有 value 属性的，可获取其文本）

// 点选框
<input type="radio/checkbox"/>  属性 checked="checked" 选中； 要创建一组复选框，只需要它们的 name 属性相同即可。

// 选择列表
<select/> 属性：size(将使选择列表变成下拉列表) ； multiple 支持多选；options 获取其选项集合  
<option/> 属性： index其在options集合中的索引位置；text列表中显示的内容； value选项定义的值
```

```
HTML5 添加了许多的新类型 type=""
color  指定颜色的控件
email
number  如果输入的不是数字，则是一个空字符串
date  yyyy-mm-dd格式
month  yyyy-mm格式
range  为不精确的数值输入创建一个内置滑块
search  单行文本输入控件
tel  
等等
```
```
HTML5 添加了许多的新属性
autofocus  页面加载时控时有焦点
pattern  一个正则表达式，检测所输入值
placeholder  提示
required  提交表单前必须填写的项
```
```html
HTML5 添加了许多的新元素
<output/> 显示计算结果
<meter/> 值的图形化显示
<progress/> 任务的已完成进度
```