# SASS与SCSS
- SASS严格缩进，SCSS是带括号的

## 插件
- 在vscode中使用插件`live sass compiler`(学习sass用的,用于将sass代码转化为普通的css代码)
  
## 基础
- 变量：`$value: 10px + 6px;`
- 嵌套与作用域

### 引入文件
- sass中通常将`变量`都写在一个单独的文件内,
- 这个文件的命名方式是`下划线 + 名字.scss`，这里的下划线表示这个文件是私有文件,不会被编译成`css`文件
- 其他同路径文件引用这个文件：` @import 'viriables'; `(不需要下划线,即使原文件下有`_下划线`)


### Minins
- 示例
  ```css
  @mixin button {  
    font-size: 1em;  
    padding: 0.5em 1.0em;  
    text-decoration: none;  
    color: #fff;  
  }
  /*混如css代码*/ 
  .button-green {  
    @include button;  
    background-color: green;  
  }
  ```
- mixins也能传参
  ```css
  @mixin button($background) {  
    font-size: 1em;  
    padding: 0.5em 1.0em;  
    text-decoration: none;  
    color: #fff;  
    background: $background;  
  }
  /*生成绿色按钮*/
  .button-green {  
    @include button(green);  
  }
  ```
- 媒体查询与mixin的配合
  ```css
  @mixin ipad {
    @media screen and (min-width: 768px) {
      /* 相当于占位符 */
      @content;
    }
  }

  .header {
    width: 1000px;
    @include ipad {
      width: 500px;
    }
  }
  .footer {
    color: blue;
    @include ipad {
      color: red;
    }
  }
  ```
  * 上面的scss代码会变成下面的代码
    ```css
    .header {
      width: 1000px;
    }

    @media screen and (min-width: 768px) {
      .header {
        width: 500px;
      }
    }
    .footer {
      color: blue;
    }

    @media screen and (min-width: 768px) {
      .footer {
        color: red;
      }
    }
    ```
  

### sass预定义函数
- lighten
  * 如:`color:lighten(#f00, 20%);`颜色将变浅20%
- darken
  * 与`lighten`同理,颜色变深

### 函数
- 格式
  ```css
  @function 函数名(形参) {
    @return;
  }
  ```
- 例子
  ```css
  @function getWidth($w) {
    @return $w * 2;
  }
  .main{
    max-width: #{getWidth(20)}px;
  }

  /*编译后*/
  .main {
    max-width: 40px;
  }
  ```
### 选择
- `if()`
  * 示例
    ```css
    @mixin test($condition) { 
      /* if(条件判断, true返回值, false返回值) */
      $color: if($condition, blue, red); 
      color:$color 
    }
      
    .firstClass { 
      @include test(true); 
    } 
    
    .secondClass { 
      @include test(false); 
    }
    ```
- `@if`后跟一个表达式，如果表达式的结果为 true，则返回特定的样式，示例如下：
  ```css
  @mixin txt($weight) { 
    color: white; 
    @if $weight == bold { font-weight: bold;} 
  } 
  
  .txt1 { 
    @include txt(none); 
  } 
  
  .txt2 { 
    @include txt(bold); 
  }
  ```
### 循环
- `@for` 指令常用于循环输出,有两种使用方式
  * `from start through end`,遍历的范围是 [start, end]
  * `from start to end`,遍历的范围是[start, end-1]
  * 如果你想实现从大到小的遍历，只需让 start 大于 end 即可
- 示例
  ```css
  @for $i from 1 through 4 { 
    .col-#{$i} { 
      width: 100/4 * $i + %;
    } 
  }
  /*结果*/ 
  .col-1 { 
    width: 25%; 
  } 
  .col-2 { 
    width: 50%; 
  } 
  .col-3 { 
    width: 75%; 
  } 
  .col-4 { 
    width: 100%; 
  }
  ```