# vue模拟hover事件

- vue中通过绑定`mouseover`和`mouseout`事件来模拟`hover`事件
  ```js
  <el-button type="primary" icon="el-icon-search" plain round 
    @mouseenter.native="knowledge = '即将推出，敬请期待'" 
    @mouseleave.native="knowledge = '知识库搜索答案'">
    {{knowledge}}
  </el-button>
  ```