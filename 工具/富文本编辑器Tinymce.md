# 富文本编辑器Tinymce

## 使用
```js
npm install tinymce
npm install @tinymce/tinymce-vue
```
```html
<template>
  <div class="postmsg">
    <div class="postmsg__line">
      <Editor id="tinymce" v-model="content" :init="editorInit"></Editor>
    </div>
  </div>
</template>

<script>
import tinymce from 'tinymce/tinymce' // 在 node_modules 里
import 'tinymce/themes/silver/theme'
import Editor from '@tinymce/tinymce-vue'
import 'tinymce/plugins/wordcount' // 引入插件
export default {
  name: 'PostMsg',
  components: {
    Editor
  },
  data() {
    return {
      content: '', // 富文本编辑器的文本
      editorInit: { // 富文本编辑器订单初始化配置
        skin_url: '/skins/ui/oxide', // 主题
        height: 300,
        width: 900,
        resize: false,
        branding: false, // 去水印
        paste_data_images: false, // 允许粘贴图像?
        menubar: false, //顶部菜单栏显示
        plugins: 'wordcount' // 插件
      },
    }
  },
  mounted() {
    tinymce.init({}) // 初始化
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped></style>
```

## 判断所输入文本的字符长度的技巧
- 因为实时监控富文本编辑器的输入内容,会发现有一堆转义字符,标签,style属性混杂
- 那么如何获取`innerHTML`的长度呢
- 发现富文本编辑器的每段文本都有`<p>`包裹
- 新建一个div,div内插入`<p>`(p即富文本编辑器里所输入的所有内容),这是`<p>`里的转义字符什么的都会被浏览器自动转义(不用你操心),再遍历div里的p的属性`innerHTML`的`length`即可完成

## image
- 貌似一行只能放一张`<img />`, 多张图片最好换行

## 参考
- [在 Vue 项目中引入 tinymce 富文本编辑器](https://www.cnblogs.com/wisewrong/p/8985471.html)
- [tinymce 5更新后，如何使用tinymce-vue](https://segmentfault.com/a/1190000018358304?utm_source=tag-newest)
- [官方文档](https://www.tiny.cloud/docs/configure/editor-appearance/#resize)