# HTML经验

- `可拖拽的布局`：Github库：vue-grid-layout
  ```js
  // 安装
  npm install vue-grid-layout --save
  // 在需要使用的组件中导入
  import VueGridLayout from 'vue-grid-layout';
  // 添加
  export default {
    components: {
      GridLayout: VueGridLayout.GridLayout,
      GridItem: VueGridLayout.GridItem
    },
    // ... data, methods, mounted (), etc.
  }
  ```
  ```html
  <!-- 注意：(例子) -->
  <!-- 在data () {}中必须添加:layout="layout"中的数据"layout" 如： -->
      <grid-layout
        :layout="layout"
        :col-num="6"      // 表示分成多少 列
        :row-height="30"  // 表示行高多少 px
        :is-draggable="true"
        :is-resizable="true"
        :vertical-compact="false"
        :use-css-transforms="true">
        <grid-item v-for="(item, index) of layout" :key="index" class="gridLayout"
          :x="item.x"
          :y="item.y"
          :w="item.w"
          :h="item.h"
          :i="item.i">
          {{item.index}}
        </grid-item>
      </grid-layout>
  ```
  ```js
  <!-- 在data() {} 中，（必须有） -->
  layout: [
  {"x":0,"y":0,"w":7,"h":1,"i":"0","maxW":7, "maxH": 1},
  {"x":0,"y":1,"w":7,"h":1,"i":"1", "maxW":7, "minH": 1},
  {"x":0,"y":2,"w":7,"h":2,"i":"2" , "minW":7, "minH": 2},
  {"x":7,"y":0,"w":13,"h":4.5,"i":"3", "minW":13, "minH": 4.5},
  ], // 布局代码
  <!-- 若想在<grid-item/>中加入自定义的组件component，则需要参考：https://www.cnblogs.com/yuwenjing0727/p/7762985.html -->
  ```
- `总加载时间` = 白屏时间(first paint) + 首屏时间 + 页面可交互(DOM Ready) + 其他资源加载完成.
  * 白屏时间（first Paint Time）——用户从打开页面开始到页面开始有东西呈现为止
  * 首屏时间——用户浏览器首屏内所有内容都呈现出来所花费的时间
  * 用户可操作时间(dom Interactive)——用户可以进行正常的点击、输入等操作，默认可以统计domready时间，因为通常会在这时候绑定事件操作
  * 总下载时间——页面所有资源都加载完成并呈现出来所花的时间，即页面 onload 的时间
- `<a>`需要标注协议
  ```html
  <a href="www.nowcoder.com">牛客网</a>  错误
  <a href="http://www.nowcoder.com/">牛客网</a> 正确
  ```
  解析： href属性值是URL，而URL里包含协议，但不一定是http协议。如果没协议就会解析为相对路径。  
  在浏览器地址栏中不输入协议名也行是因为浏览器发现这个相对路径没东西之后自动帮你加的协议
- `置换元素`与`不可置换元素`
  * 置换元素：浏览器根据元素的标签和属性，来决定元素的具体显示内容。 
    - 例如：浏览器会根据`<img>`标签的src属性的值来读取图片信息并显示出来，而如果查看(x)html代码，则看不到图片的实际内容；
    - 置换元素在其显示中生成了框，这也就是有的内联元素能够设置宽高的原因。
  * 不可替换元素：(x)html 的大多数元素是不可替换元素，即其内容直接表现给用户端（如浏览器）。
    - 例如： `<label>label中的内容</label>` 标签`<label>`是一个非置换元素，文字label中的内容”将全被显示
- 网页最上层的标题应该是title，文章最上层的标题才是h1
  * 一般网页设计中logo部分会由h1标签组成，主要是由于seo的优化，方便爬虫抓取网页信息，由此可见，爬虫会优先抓取`<h>`系列标签，像常见的`p`标签也会优先抓取
- `<a>`标签
  - `<a href="chap3.html" target="view_window">Chapter 3</a>`
  - `<a>`标签的 target 属性, 表示在哪里打开新界面。它有4个默认值，也支持自定义。
  - target的值：    
    * _blank         在新窗口打开链接
    * _self            在当前框架中打开链接
    * _parent       在父框架打开链接
    * _top             在当前窗口打开链接
    * framename  在指定框架打开链接
- 标签嵌套
  * ul只能紧挨着li; 
  * a中不能再嵌套a; 
  * ol li是有序排列
  * ```html
    <dl>
      <dt></dt>
      <dd></dd>
    </dl>
    ```
- css与js文件的加载顺序  
  * link标签是同时加载的 script标签才会加载完一个再加载另一个
  * 所以css加载是并行的，js解析是按顺序的（注意加载以及解析的区别）
- 不用margin以及padding来确定界面布局，因为自适应  
  假如使用margin，那么在调整浏览器分辨率(即不同设备情况下)，展示效果将不同
-对 html 以及 body 的修改全部放在 reset.css 中
- 每个子页面的最外部使用`<div id=""/>`，该子页面的内部使用 `class=""` 对`<div/>` 进行标注，这样是方便后续样式修改
- 修改元素的disabled状态
  ```
  element.disabled = false
  ```
- 考题
  ```js
  var msg='hello'; 
  for (var i=0; i<10; i++)
   { 
     var msg='hello'+i*2+i; 
   }
   alert(msg); 
  最后一句alert的输出结果是？  Hello189
  这道题的真实考点其实是：for循环不是函数，因此for内部定义的msg与for外部定义的msg为在同一块作用域，为同一个变量。
  ```
- CSS中可以和不可以继承的属性  
  ```css
  一、无继承性的属性
  1、display：规定元素应该生成的框的类型
  2、文本属性：
  vertical-align：垂直文本对齐
  text-decoration：规定添加到文本的装饰
  text-shadow：文本阴影效果
  white-space：空白符的处理
  unicode-bidi：设置文本的方向
  3、盒子模型的属性：width、height、margin 、margin-top、margin-right、margin-bottom、margin-left、border、  border-style、
  border-top-style、border-right-style、border-bottom-style、border-left-style、border-width、  border-top-width、border-right-right、border-bottom-width、border-left-width、
  border-color、border-top-color、border-right-color、border-bottom-color、border-left-color、border-top、  border-right、border-bottom、border-left、padding、padding-top、padding-right、padding-bottom、padding-left
  4、背景属性：background、background-color、background-image、background-repeat、background-position、  background-attachment
  5、定位属性：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、  overflow、clip、z-index
  6、生成内容属性：content、counter-reset、counter-increment
  7、轮廓样式属性：outline-style、outline-width、outline-color、outline
  8、页面样式属性：size、page-break-before、page-break-after
  9、声音样式属性：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during
  二、有继承性的属性
  1、字体系列属性
  font：组合字体
  font-family：规定元素的字体系列
  font-weight：设置字体的粗细
  font-size：设置字体的尺寸
  font-style：定义字体的风格
  font-variant：设置小型大写字母的字体显示文本，这意味着所有的小写字母均会被转换为大写，但是所有使用小型大写字体的字母  与其余文本相比，其字体尺寸更小。
  font-stretch：对当前的 font-family 进行伸缩变形。所有主流浏览器都不支持。
  font-size-adjust：为某个元素规定一个 aspect 值，这样就可以保持首选字体的 x-height。
  2、文本系列属性
  text-indent：文本缩进
  text-align：文本水平对齐
  line-height：行高
  word-spacing：增加或减少单词间的空白（即字间隔）
  letter-spacing：增加或减少字符间的空白（字符间距）
  text-transform：控制文本大小写
  direction：规定文本的书写方向
  color：文本颜色
  3、元素可见性：visibility
  4、表格布局属性：caption-side、border-collapse、border-spacing、empty-cells、table-layout
  5、列表布局属性：list-style-type、list-style-image、list-style-position、list-style
  6、生成内容属性：quotes
  7、光标属性：cursor
  8、页面样式属性：page、page-break-inside、windows、orphans
  9、声音样式属性：speak、speak-punctuation、speak-numeral、speak-header、speech-rate、volume、voice-family、  pitch、pitch-range、stress、richness、、azimuth、elevation
  三、所有元素可以继承的属性
  1、元素可见性：visibility
  2、光标属性：cursor
  四、内联元素可以继承的属性
  1、字体系列属性
  2、除text-indent、text-align之外的文本系列属性
  五、块级元素可以继承的属性
  1、text-indent、text-align
  ```
- DOCTYPE
  1. <!DOCTYPE> 声明位于文档中的最前面，处于 <html> 标签之前。告知浏览器的解析器，用什么文档类型 规范来解析这个文档。
  2. 严格模式的排版和 JS 运作模式是 以该浏览器支持的最高标准运行。在混杂模式(兼容模式)中，页面以宽松的向后兼容的方式显示。模拟老式浏览器的行为以防止站点无法工作。
  3. DOCTYPE不存在或格式不正确会导致文档以混杂模式呈现。
- 原生HTML中不支持冒泡的事件有？  
  blur、focus、load、unload 、以及自定义的事件。  
  原因是在于：这些事件仅发生于自身上，而它的任何父节点上的事件都不会产生，所有不会冒泡。  
  在Vue中，使用`@click.prevent="xxx"`以及`@click.stop="xxx"`进行阻止默认事件以及防止事件冒泡
- `<a>`标签在进行页面跳转时,最好带"协议",否则默认把链接接在当前链接之后  
  代码`window.open(链接)`在新页面打开链接  
  代码`window.location.href;//当前页面打开URL页面`    
- 前端在使用图片时,需要压缩图片,一般颜色多的选择`JPG`格式,颜色少的选择`PNG`格式

