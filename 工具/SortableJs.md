# SortableJs

## 配置项
```js
var sortable = new Sortable(el, {
  group: "name",  // or { name: "...", pull: [true, false, 'clone', array], put: [true, false, array] }
  sort: true,  // boolean 定义是否列表单元是否可以在列表容器内进行拖拽排序
  delay: 0, // number 定义鼠标选中列表单元可以开始拖动的延迟时间；
  touchStartThreshold: 0, // px, how many pixels the point should move before cancelling a delayed drag event
  disabled: false, // boolean 定义是否此sortable对象是否可用，为true时sortable对象不能拖放排序等功能，为false时为可以进行排序，相当于一个开关；
  store: null,  // @see Store
  animation: 150,  // ms, number 单位：ms，定义排序动画的时间
  easing: "cubic-bezier(1, 0, 0, 1)", // Easing for animation. Defaults to null. See https://easings.net/ for examples.
  handle: ".my-handle",  // 格式为简单css选择器的字符串，使列表单元中符合选择器的元素成为拖动的手柄，只有按住拖动手柄才能使列表单元进行拖动
  filter: ".ignore-elements",  // 过滤器，不需要进行拖动的元素
  preventOnFilter: true, //  在触发过滤器`filter`的时候调用`event.preventDefault()`
  draggable: ".item",  // 允许拖拽的项目类名
  ghostClass: "sortable-ghost",  // drop placeholder的css类名
  chosenClass: "sortable-chosen",  // 被选中项的css 类名
  dragClass: "sortable-drag",  // 正在被拖拽中的css类名
  dataIdAttr: 'data-id',

  swapThreshold: 1, // Threshold of the swap zone
  invertSwap: false, // Will always use inverted swap zone if set to true
  invertedSwapThreshold: 1, // Threshold of the inverted swap zone (will be set to swapThreshold value by default)
  direction: 'horizontal', // 拖拽方向 (默认情况下会自动判断方向)

  forceFallback: false,  // 忽略 HTML5拖拽行为，强制回调进行

  fallbackClass: "sortable-fallback",  // 当使用forceFallback的时候，被复制的dom的css类名
  fallbackOnBody: false,  // 将cloned DOM 元素挂到body元素上。
  fallbackTolerance: 0, // Specify in pixels how far the mouse should move before it's considered as a drag.

  scroll: true, // or HTMLElement
  scrollFn: function(offsetX, offsetY, originalEvent, touchEvt, hoverTargetEl) { ... }, // if you have custom scrollbar scrollFn may be used for autoscrolling
  scrollSensitivity: 30, // px, how near the mouse must be to an edge to start scrolling.
  scrollSpeed: 10, // px
  bubbleScroll: true, // apply autoscroll to all parent elements, allowing for easier movement

  dragoverBubble: false,
  removeCloneOnHide: true, // Remove the clone element when it is not showing, rather than just hiding it
  emptyInsertThreshold: 5, // px, distance mouse must be from empty sortable to insert drag element into it


  setData: function (/** DataTransfer */dataTransfer, /** HTMLElement*/dragEl) {
    dataTransfer.setData('Text', dragEl.textContent); // `dataTransfer` object of HTML5 DragEvent
  },

  // 元素被选中
  onChoose: function (/**Event*/evt) {
    evt.oldIndex;  // element index within parent
  },

  // 元素未被选中的时候（从选中到未选中）
  onUnchoose: function(/**Event*/evt) {
    // same properties as onEnd
  },

  // 开始拖拽的时候
  onStart: function (/**Event*/evt) {
    evt.oldIndex;  // element index within parent
  },

  // 结束拖拽
  onEnd: function (/**Event*/evt) {
    var itemEl = evt.item;  // dragged HTMLElement
    evt.to;    // target list
    evt.from;  // previous list
    evt.oldIndex;  // element's old index within old parent
    evt.newIndex;  // element's new index within new parent
    evt.clone // the clone element
    evt.pullMode;  // when item is in another sortable: `"clone"` if cloning, `true` if moving
  },

  // 元素从一个列表拖拽到另一个列表
  onAdd: function (/**Event*/evt) {
    // same properties as onEnd
  },

  // 列表内元素顺序更新的时候触发
  onUpdate: function (/**Event*/evt) {
    // same properties as onEnd
  },

  // 列表的任何更改都会触发
  onSort: function (/**Event*/evt) {
    // same properties as onEnd
  },

  // 元素从列表中移除进入另一个列表
  onRemove: function (/**Event*/evt) {
    // same properties as onEnd
  },

  // 试图拖拽一个filtered的元素
  onFilter: function (/**Event*/evt) {
    var itemEl = evt.item;  // HTMLElement receiving the `mousedown|tapstart` event.
  },

  // 拖拽移动的时候
  onMove: function (/**Event*/evt, /**Event*/originalEvent) {
    // Example: https://jsbin.com/nawahef/edit?js,output
    evt.dragged; // dragged HTMLElement
    evt.draggedRect; // DOMRect {left, top, right, bottom}
    evt.related; // HTMLElement on which have guided
    evt.relatedRect; // DOMRect
    evt.willInsertAfter; // Boolean that is true if Sortable will insert drag element after target by default
    originalEvent.clientY; // mouse position
    // return false; — for cancel
    // return -1; — insert before target
    // return 1; — insert after target
  },

  // clone一个元素的时候触发
  onClone: function (/**Event*/evt) {
    var origEl = evt.item;
    var cloneEl = evt.clone;
  },

  // 拖拽元素改变位置的时候
  onChange: function(/**Event*/evt) {
    evt.newIndex // most likely why this event is used is to get the dragging element's current index
    // same properties as onEnd
  }
});
```             
- group：string or object
  ```js
  string：命名，个人建议用元素id就行,用处是为了设置可以拖放容器时使用，在array中的put的设置中再做介绍；
  object：{name,pull,put}
          name：同string的方法，
          pull：pull用来定义从这个列表容器移动出去的设置，true/false/'clone'/function
              true:列表容器内的列表单元可以被移出；
              false：列表容器内的列表单元不可以被移出；
              'clone'：列表单元移出，移动的为该元素的副本；
              function：用来进行pull的函数判断，可以进行复杂逻辑，在函数中return false/true来判断是否移出；
          put：put用来定义往这个列表容器放置列表单元的的设置，true/false/['foo','bar']/function
              true:列表容器可以从其他列表容器内放入列表单元；
              false：与true相反；
              ['foo','bar']：这个可以是一个字符串或者是字符串的数组，代表的是group配置项里定义的name值；
              function：用来进行put的函数判断，可以进行复杂逻辑，在函数中return false/true来判断是否放入；
  ```
                        
- sort：boolean 定义是否列表单元是否可以在列表容器内进行拖拽排序；
- delay：number 定义鼠标选中列表单元可以开始拖动的延迟时间；
- disabled：boolean 定义是否此sortable对象是否可用，为true时sortable对象不能拖放排序等功能，为false时为可以进行排序，相当于一个开关；
- animation：number 单位：ms，定义排序动画的时间；
- handle：selector 格式为简单css选择器的字符串，使列表单元中符合选择器的元素成为拖动的手柄，只有按住拖动手柄才能使列表单元进行拖动；
- filter：selector 格式为简单css选择器的字符串，定义哪些列表单元不能进行拖放，可设置为多个选择器，中间用“，”分隔；
- draggable：selector 格式为简单css选择器的字符串，定义哪些列表单元可以进行拖放
- ghostClass：selector 格式为简单css选择器的字符串，当拖动列表单元时会生成一个副本作为影子单元来模拟被拖动单元排序的情况，此配置项就是来给这个影子单元添加一个class，我们可以通过这种方式来给影子元素进行编辑样式；
- chosenClass：selector 格式为简单css选择器的字符串，当选中列表单元时会给该单元增加一个class；
- forceFallback：boolean 如果设置为true时，将不使用原生的html5的拖放，可以修改一些拖放中元素的样式等；
- fallbackClass：string 当forceFallback设置为true时，拖放过程中鼠标附着单元的样式；
- scroll：boolean 默认为true，当排序的容器是个可滚动的区域，拖放可以引起区域滚动
  
## 事件
- onChoose：function 列表单元被选中的回调函数
- onStart：function 列表单元拖动开始的回调函数
- onEnd：function 列表单元拖放结束后的回调函数
- onAdd：function 列表单元添加到本列表容器的回调函数
- onUpdate：function 列表单元在列表容器中的排序发生变化后的回调函数
- onRemove：function 列表元素移到另一个列表容器的回调函数
- onFilter：function 试图选中一个被filter过滤的列表单元的回调函数
- onMove：function 当移动列表单元在一个列表容器中或者多个列表容器中的回调函数
- onClone：function 当创建一个列表单元副本的时候的回调函数
  
## 事件对象 - (事件对象在各个函数中略有不同，可通过输出对象查看对象的属性，下面简单列举几个)
- to：HTMLElement--移动到列表容器
- from：HTMLElement--来源的列表容器
- item：HTMLElement--被移动的列表单元
- clone：HTMLElement--副本的列表单元
- oldIndex：number/undefined--在列表容器中的原序号
- newIndex：number/undefined--在列表容器中的新序号

## 方法
### option(name[,value])
获得或者设置项参数，使用方法类似于jQuery用法，没有第二个参数为获得option中第一个参数所对应的值，有第二个参数时，将重新赋给第一个参数所对应的值；
### closest
没理解
### toArray()
序列化可排序的列表单元的data-id（可通过配置项中dataIdAttr修改）放入一个数组，并返回这个数组中
### sort()
通过自定义列表单元的data-id的数组对列表单元进行排序
### save()
destroy()

## 注意
- 拖拽后视图出错
  * 需要指定`row-key`
  * `<el-table :data="tableData" row-key="project_id">`

## 参考
- [中文文档](http://www.sortablejs.com/options.html)