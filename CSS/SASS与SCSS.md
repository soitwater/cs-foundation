# SASS与SCSS
- SASS严格缩进，SCSS是带括号的

## 插件
- 在vscode中使用插件`live sass compiler`
  
## 基础
- 变量：`$value: 10px;`
- 嵌套与作用域


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