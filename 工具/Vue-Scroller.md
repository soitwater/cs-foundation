# Vue-Scroller

## 安装
```shell
npm i vue-scroller -S
```

## 在组件内引入
```js
import Vue from 'vue'
import VueScroller from 'vue-scroller'
Vue.use(VueScroller)
```

## 书写示例
```html
<template>
  <div class="spot-list">
    <scroller style="height: 24vh; background:red;" :on-refresh="refresh" :on-infinite="infinite" ref="myscroller">
      <div style="background:green;height: 5vh;" v-for="(item,i) in list"  :key="i">{{item.name}}</div>
    </scroller>
  </div>
</template>

<script>
import Vue from 'vue'
import VueScroller from 'vue-scroller'
Vue.use(VueScroller)
export default {
  data() {
    return {
      list: [],
      scroller: {
        page: 1,
        pageSize: 3,
        noData: false
      }
    }
  },
  mounted() {
    this.getData() // 初始时没有数据,故需要手动调用一次
  },
  methods: {
    getData(done) {
      if(this.scroller.page == 1) {
        // 初始化数据
        this.list = [
          { name: Math.ceil(Math.random()*26) },
          { name: Math.ceil(Math.random()*26) },
          { name: Math.ceil(Math.random()*26) },
          { name: Math.ceil(Math.random()*26) },
        ]
        this.$refs.myscroller.finishInfinite(false) // 启用上拉加载更多, false 表示还有数据,可以再次调用
        this.scroller.noData = false // 还可以加载更多数据
      } else {
        if (this.scroller.page - 1> this.scroller.pageSize) {
          this.scroller.noData = true
          this.loadingTips = "没有更多数据了"
          done && done(true) // 停止上拉，显示没有更多数据
          this.$refs.myscroller.finishInfinite(true) //这个方法是不让它加载了，显示“没有更多数据”，要不然会一直转圈圈
        } else {
          this.list = this.list.concat([
            { name: Math.ceil(Math.random()*26) },
            { name: Math.ceil(Math.random()*26) },
            { name: Math.ceil(Math.random()*26) },
            { name: Math.ceil(Math.random()*26) },
          ])
          done && done(false) // 允许下次继续(触发上拉获取)
          this.$refs.myscroller.finishInfinite(false) // 关闭上拉加载更多
        }
      }
    },
    // 下拉刷新
    refresh(done) {
      let self = this
      this.scroller.page = 1
      this.getData()
    },

    // 上拉获取
    infinite(done) {
      let self = this
      if(!this.noData) {
        this.scroller.page++ //下拉一次页数+1
        setTimeout(() => {
          this.getData(done)
        }, 1000);
      }
    },
  }
}
</script>
```

## 参考
- [各个无限加载组件的对比](https://gitee.com/yangqihua/vue-scroll-mobile-demo)
- [github地址](https://github.com/wangdahoo/vue-scroller)