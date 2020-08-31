# CSS经验
## 响应式设计
- 响应式设计, 一般先适配小屏幕, 再适配大屏幕
  ```js
  // 设置移动端以及pc端两套css代码, 方法:
  mounted () {
    if(document.body.clientWidth<787){
      window.location.href='mindex.html';
    };
    //当然随时改变屏幕大小也会改变的，所以也要加上
    window.addEventListener('resize',()=>{
     if(document.body.clientWidth<787){
      window.location.href='mindex.html';
    }
  }
  ```
- 一般需要一份 `reset.css` 文件覆盖大部分样式  
  如另`<html> <body>以及Vue中的App.vue的#app{height: 100%}`
- 光圈特效：(仅新版本的浏览器支持改特性)  
  `background: radial-gradient(#fff 0%, rgba(243, 206, 205, .4) 35%, rgba(88, 88, 88, .1) 60%, #ffff 70%);`
- iconfont使用
  * 使用阿里iconfont，避免图片太多，请求太多次。    
  * [参考](https://www.cnblogs.com/hjvsdr/p/6639649.html)
  * 把需求的icon放进购物车，再放进自己的项目中，之后在"我的项目"，选"Font class"——下载至本地  
  * 解压——下载之后里面有使用方法的说明——或者[参考](https://blog.csdn.net/wangzy0327/article/details/78379496)
  * 第二种font class是：先在`main.js` 中 `import "./assets/styles/iconfont.css"` (路径自定义) —— 放好iconfont.css —— 在 iconfont.css 里有url 属性，已经说明了 iconfont.css 依赖哪些文件  
  * 第二种的使用：`<span class='iconfont icon-logo'></span> 或者 <span class='iconfont'>&#xe617;</span>` (编号在 iconfont.css 中查看)  
  * 注意：之所以iconfont在下载icon之前会新建项目，是因为下载时，会把所有的icon都放在一份css中
- iconfont 使用2（2019/12/16推荐）
  ```js
  1. 在官网项目里点击生成代码
  2. 在浏览器打开代码链接，复制代码，并粘贴到`src/assets/iconfont/index.js`
  3. 新建`src/assets/iconfont/index.css`，并粘贴
     .icon {
        width: 1em; height: 1em;
        vertical-align: -0.15em;
        fill: currentColor;
        overflow: hidden;
      }
  4. 在需要使用图标的地方使用
     <svg class="icon" aria-hidden="true">
       <use xlink:href="#icon-xxx"></use>
     </svg>
     其中的#icon-xxx里的xxx可以在官网的项目中点击“复制代码”查看
  5. 注意去色： 在“批量操作”——“批量去色”中去除原图标的颜色，这样才能通过“color”属性自由控制图标颜色
  6. 在react中是在 index.js 中的引入方式：
     const scriptElem = document.createElement('script');
     scriptElem.src = 'http://at.alicdn.com/t/font_1565678_2r28gbd0jop.js';
     document.body.appendChild(scriptElem);

  7. 在react中注意属性`className`与`xlinkHref`
  8. 参考：https://zhuanlan.zhihu.com/p/88592868
  ```
- `@import`  
  CSS中link和@import的区别
  1. `@import url（）`机制是不同于link的，link是在加载页面前把css加载完毕，而`@import url（）`则是读取完文件后在加载，所以会出现一开始没有css样式，闪烁一下出现样式后的页面(网速慢的情况下)。  
  2. `@import` 是css2里面的，所以古老的ie5不支持。  
  3. 当使用javascript控制dom去改变样式的时候，只能使用link标签，因为`@import`不是dom可以控制的。
  4. link除了能加载css外还能定义RSS，定义rel连接属性，`@import`只能加载css  
- CSS3新增属性
  * box-shadow（阴影效果）
  * border-color（为边框设置多种颜色）
  * border-image（图片边框）
  * text-shadow（文本阴影）
  * text-overflow（文本截断）
  * word-wrap（自动换行）
  * border-radius（圆角边框）
  * opacity（透明度）
  * box-sizing（控制盒模型的组成模式）
  * resize（元素缩放）
  * outline（外边框）
  * background-size（指定背景图片尺寸）
  * background-origin（指定背景图片从哪里开始显示）
  * background-clip（指定背景图片从什么位置开始裁剪）
  * background（为一个元素指定多个背景）
  * hsl（通过色调、饱和度、亮度来指定颜色颜色值）
  * hsla（在hsl的基础上增加透明度设置）
  * rgba（基于rgb设置颜色，a设置透明度）
## 倾斜字体
- 实现字体倾斜的方法：字体外层盒子`transform:rotate`
- 移动端页面只要兼容 Chrome 和 Safari，所以浏览器标签只考虑`-webkit-`
## 滚动条样式
- 隐藏滚动条  
  * [纯CSS实现隐藏滚动条但仍具有滚动的效果](https://blog.csdn.net/huangpb123/article/details/79346163)  
  * [CSS 实现隐藏滚动条同时又可以滚动](https://www.cnblogs.com/alice626/p/6206760.html)  
  * 移动端  
    ```css
    .container::-webkit-scrollbar {
      display: none;
    }
    ```
  * 桌面端  
    - > 大致思路就是在内容div外面包一个父容器div，设置 overflow: hidden，  
    - > 内容div设置 display-x: hidden; display-y: auto;   
    - > 最后设置父容器div的宽度小于内容div的宽度或者设置内容div的 margin-right 为负值就可以了。  
- css侧边滚动条样式美化(chrome浏览器)
  ```css
  /*整体 */ 
  ::-webkit-scrollbar {
    width: 8px; 
    height: 1px; 
  } 
  /*滑块 */
  ::-webkit-scrollbar-thumb { 
    border-radius   : 10px;
    background-color: skyblue;
    background-image: -webkit-linear-gradient(
      45deg,
      rgba(255, 255, 255, 0.2) 25%,
      transparent 25%,
      transparent 50%,
      rgba(255, 255, 255, 0.2) 50%,
      rgba(255, 255, 255, 0.2) 75%,
      transparent 75%,
      transparent);
  } 
  /*背景 */
  ::-webkit-scrollbar-track { 
    background-color: #ededed; 
    border-radius: 10px;
    box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2); 
  }
  ```
## 伪元素
- 修改css伪元素内容(before, after)
  * 添加`<style>`节点
    ```js
    var css=function(t,s){
      s=document.createElement('style');
      s.innerText=t;
      document.body.appendChild(s);
    };
    ```
  * 增删className
  * 利用伪元素的content属性可以读取元素的`data`属性这一特点[js如何控制css伪元素内容（before，after）](https://www.cnblogs.com/yunkou/p/4269974.html)
- js动态创建样式: style 节点和 link 节点
  * [js动态创建样式: style 和 link](https://www.cnblogs.com/stephenykk/p/5406614.html)
- 定高,定宽可能导致`padding`无效
- `div[data-size~="mini"]` 与 `div [data-size~="mini"]`的区别
- 在写样式时，不推荐使用margin，因为margin可能造成页面撑开,纵向还好，但横向撑开真的很难发现，所以最好不用margin
- vertical-align: text-bottom 
- 2019年的fastclick
  * fastclick 原理
    - ```
      移动端，当用户点击屏幕时，会依次触发 touchstart，touchmove(0 次或多次)，touchend，mousemove，mousedown，mouseup，click。 touchmove 。只有当手指在屏幕发生移动的时候才会触发 touchmove 事件。在 touchstart ，touchmove 或者 touchend 事件中的任意一个调用 event.preventDefault，mouse 事件 以及 click 事件将不会触发。
      ```
    - ```
      fastClick 在 touchend 阶段 调用 event.preventDefault，然后通过 document.createEvent 创建一个 MouseEvents，然后 通过 event​Target​.dispatch​Event 触发对应目标元素上绑定的 click 事件。
      ```
  * [2019 再聊移动端 300ms 延迟及 fastClick 原理解析](https://zhuanlan.zhihu.com/p/66845055)
  * 解决方案 - 禁用缩放
    ```css
    <meta name="viewport" content="user-scalable=no" />
    或者
    html {
      touch-action: manipulation;
      touch-action: manipulation; // IE11+
      -ms-touch-action: manipulation; // IE10
    }
    ```
- padding 溢出宽度解决：`box-sizing: border-box;`
- 设置内联style时,例如background,会导致写在`<style>`里的该元素的`:hover`的background属性也失效,因为内联style覆盖了hover的background
- `text-overflow: ellipsis;`不生效
  * [参考](https://segmentfault.com/q/1010000006781835)
  * ```css
    * {
      text-overflow: ellipsis;
      overflow: hidden;
      word-space:nowrap;
      还需要设置 width
    }
    ```
- 即使在`<style>`上使用了`scoped`或者`module`,那么依然可能由于css选择器是标签选择器而导致修改了全局的标签的css属性，如：
  ```css
  <style scoped>
    div {
      background: #f00; /*会全局修改而不是局部修改*/
    }
  </style>
  ```
- 在`<style>`使用`stylus`或`scss`或`less`等, 最好写上类似的`<style lang='stylus'>`,避免编辑器报错
- line-height
  * [参考:张鑫旭]()
  * 英文字母有四线谱: 从上到下依次是(顶线,中线,基线,底线), line-height 表示的就是两行文字之间基线的距离
  * `vertical-align`的4个属性值`top, middle, baseline, bottom`四个值与上述的四线谱之间存在关联
  * div的高度由2个属性决定`height`或`line-height`,经常看到`<div>123</div>`明明div的 height 为0,但看起来就是有高度,这是由于默认`line-height`属性值的存在
  * `line-height`的特点
    - `line-height`取一行的最高值,例如`<span style="line-height:20px;">取手下line-height<span style="line-height:40px;">最高</span>的值</span>`这一行的实际`line-height`为40px
    - 垂直居中性: 不管外层 div 是 height 为0还是多少, div 内的文字都会垂直居中排列