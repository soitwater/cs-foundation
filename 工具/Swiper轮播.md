# Swiper轮播

## 安装
`cnpm i -S vue-awesome-swiper`

## 使用
- 抽出组件
```html
<template>
  <div id="swiper">
    <swiper :options="swiperOption">
      <swiper-slide class="swiper-slide-lastBround" v-for="(item, index) in html" :key="index" v-html="item"></swiper-slide>
    </swiper>
    <div class="swiper-pagination-bround"><div class="swiper-pagination"></div></div>
    <div class="swiper-change-page swiper-button-next iconfont" style="margin-right: 4%;">&#xe61b;</div> 
    <div class="swiper-change-page swiper-button-prev iconfont" style="margin-left : .4%;">&#xe620;</div>
  </div>
</template>

<script>
import "swiper/dist/css/swiper.css"
import {swiper, swiperSlide} from "vue-awesome-swiper"
export default {
  name: "Swiper",
  data () {
    return {
      swiperOption: {
        loop: true,
        autoplay: {
          delay: 4000, //自动切换的时间间隔，单位ms
          stopOnLastSlide: true, //如果设置为true，当切换到最后一个slide时停止自动切换。
          disableOnInteraction: false, //用户操作swiper之后，是否禁止autoplay。
          reverseDirection: false, //开启反向自动轮播。
          waitForTransition: true, //等待过渡完毕。自动切换会在slide过渡完毕后才开始计时。
        },
        speed: 1000,
        direction: "horizontal",
        grabCursor: true, // 小手掌抓取滑动
        pagination: {
          el: ".swiper-pagination",
          clickable: true,
          type: "bullets",
        },
        navigation: {
          nextEl: '.swiper-button-next',
          prevEl: '.swiper-button-prev',
        },
      }
    }
  },
  components: {
    swiper,
    swiperSlide,
  },
}
</script>
```

## 参考
- [swiper实现臭美app滑动效果](https://www.cnblogs.com/leaf930814/p/7371916.html)
- [官方文档](https://github.surmon.me/vue-awesome-swiper/)