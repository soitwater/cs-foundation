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

## 在vue中使用
### tinymce.vue
```js
<template>
  <div class="postmsg">
    <div class="postmsg__line">
      <!-- document.querySelector('.postmsg__line textarea') -->
      <!-- <el-popover class="tip" placement="left" trigger="hover" width="500">
        <div style="font-size:14Px;">
          <div style="font-weight:700;">温馨提示</div>
          <li>
            <span style="color:#f97828;">图片不与文字共一行</span>，图片应独立占一行, 上传图片前注意要换行
          </li>
          <li>多余的空行注意删掉</li>
          <li>
            选择"工具栏"中的
            <span style="color:#f97828;">"段落样式"</span>可以为文本添加预设样式
            <br />操作方式为：选择一个段落 —— 点击"工具栏"的"段落样式" —— 选择其中某一项样式
          </li>
          <li>
            建议在
            <span style="color:#f97828;">word文档</span>处理完所有格式后, 再粘贴进富文本编辑器内
          </li>
          <li>不要在富文本编辑器内使用超链接</li>
          <li></li>
        </div>
        <div circle type="text" icon="el-icon-question" slot="reference">?</div>
      </el-popover>-->
      <editor :id="selectorId" v-model="content" :init="init" :disabled="disabled"></editor>
    </div>
  </div>
</template>

<script>
import tinymce from "tinymce/tinymce";
import Editor from "@tinymce/tinymce-vue";
import "tinymce/themes/silver";
import "tinymce/plugins/image"; // 上传图片插件
import "tinymce/plugins/lists";
import "tinymce/plugins/paste";
import "tinymce/plugins/preview";
import "tinymce/plugins/code";
import "tinymce/plugins/link/index.js";
import "tinymce/plugins/autosave";
import "tinymce/plugins/autolink";
import "tinymce/plugins/lineheight/plugin.min.js";
import "tinymce/icons/default/icons"; // 避免报错
import globalSetting from "@/settings.js"; // 上传图片的目标地址
import { uploadImgApi } from "@/api/Request/modules/upload.js";
import { myButton } from "./util.js";
import vm from "@/main.js";

export default {
  name: "Tinymce",
  components: {
    Editor,
  },
  watch: {
    // 富文本编辑器的文本
    content: {
      handler(val, oldVal) {
        if (val === oldVal) {
          return;
        }
        this.$emit("content-tinymce", this.content);
      },
    },
    // 不要删除,编辑页进入时拿不到数据
    contentText: {
      handler(val, oldVal) {
        this.content = val;
      },
    },
  },
  props: {
    disabled: {
      type: Boolean,
      default: false,
    },
    plugins: {
      type: [String, Array],
      default: function () {
        return "lists image paste preview link code lineheight autosave autolink";
      },
    },
    toolbar: {
      type: [String, Array],
      default: function () {
        return [
          `styleselect | presuppositionTemplate | lineheight | forecolor backcolor |bold italic underline strikethrough | outdent indent  alignleft aligncenter alignright alignjustify alignnone | `,
          ` |undo redo restoredraft | image link removeformat | fontselect | fontsizeselect| blockquote subscript superscript | bullist numlist |copy paste pastetext print code  preview helpToolTip`,
        ];
      },
    },
    // 编辑页面进入时，传进来的文本
    contentText: {},
    // 不同的id才能在同一页面使用多个 tinymce
    selectorId: {},
    // 编辑区样式, 不会被提交
    content_style: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      content: this.contentText, // 富文本编辑器的文本
      init: {
        selector: "#" + this.selectorId,
        skin_url: "./tinymce/skins/ui/oxide", // 主题：放在static 或者 public 中的; process.env.VUE_APP_PREFIX +
        language_url: "./tinymce/langs/zh_CN.js", // 语言包的路径
        language: "zh_CN", //语言
        fontsize_formats: "11px 12px 14px 16px 18px 24px 36px 48px",
        font_formats:
          "微软雅黑='微软雅黑';宋体='宋体';黑体='黑体';仿宋='仿宋';楷体='楷体';隶书='隶书';幼圆='幼圆';Andale Mono=andale mono,times;Arial=arial,helvetica,sans-serif;Arial Black=arial black,avant garde;Book Antiqua=book antiqua,palatino;Comic Sans MS=comic sans ms,sans-serif;Courier New=courier new,courier;Georgia=georgia,palatino;Helvetica=helvetica;Impact=impact,chicago;Symbol=symbol;Tahoma=tahoma,arial,helvetica,sans-serif;Terminal=terminal,monaco;Times New Roman=times new roman,times;Trebuchet MS=trebuchet ms,geneva;Verdana=verdana,geneva;Webdings=webdings;Wingdings=wingdings",
        // placeholder: "",
        height: 800,
        autosave_retention: "30m", // 草稿有效期
        custom_undo_redo_levels: 50, // 最大撤销次数, 节约内存
        resize: false,
        link_context_toolbar: true, // 超链接编辑
        statusbar: false, // 去掉状态栏
        branding: false, // 去水印
        paste_data_images: true, // 允许粘贴图像
        menubar: false, //顶部菜单栏显示
        plugins: this.plugins, // 插件
        toolbar: this.toolbar, // 工具栏
        paste_preprocess: function (plugin, args) {
          console.log("禁止粘贴: ", args.content);
          args.content = '';
        },
        images_upload_handler: function (blobInfo, succFun, failFun) {
          let form = new FormData();
          form.append("file", blobInfo.blob()); //转化为易于理解的file对象
          uploadImgApi(form)
            .then((res) => {
              let { message, status, result } = res.data;
              if (status === 200) {
                succFun(result.linkUrl);
              } else {
                throw new Error("服务器返回失败信息");
              }
            })
            .catch((err) => {
              console.error("图片上传失败\n", err);
            });
        },
        image_description: false, 
        image_uploadtab: true, 
        image_class_list: [{ title: "默认", value: "picture" }], 
        content_style: this.content_style,
        style_formats: [
          {
            title: "[常见问题]:灰色背景段落",
            selector: "p,h1,h2,h3,h4,h5,h6,td,th,div,ul,ol,li,table,img",
            classes: "text",
          },
          {
            title: "[常见问题]:无色背景段落",
            selector: "p,h1,h2,h3,h4,h5,h6,td,th,div,ul,ol,li,table,img",
            classes: "without-bg",
          },
          {
            title: "[内容页]:段落",
            selector: "p,h1,h2,h3,h4,h5,h6,td,th,div,ul,ol,li,table,img",
            classes: "content-page--p",
          },
          {
            title: "[内容页]:小标题(无下边距)",
            selector: "p,h1,h2,h3,h4,h5,h6,td,th,div,ul,ol,li,table,img",
            classes: "content-page--none-margin-bottom ",
          },
        ],
        style_formats_merge: true,
        style_formats_autohide: true,
        setup: myButton,
      },
    };
  },
  mounted() {
    tinymce.init({}); 
  },
  methods: {
  },
};
</script>

<style lang="stylus" scoped>
.postmsg {
  $line = 28px;

  .postmsg__line {
    .tip {
      display: inline-block;
      width: $line;
      height: $line;
      border-radius: 50%;
      text-align: center;
      line-height: $line;
      position: absolute;
      font-size: 24px;
      font-weight: 300;
      color: #fff;
      top: 0;
      right: 0;
      background: #f97828;
      z-index: 100;

      &:hover {
        opacity: 0.8;
      }
    }
  }
}
</style>
```

### util.js
```js
import vm from "@/main.js";

/**
 * @description - 修改注意：先在HTML模板修改,标签+样式;
 * 把标签+样式分别替换掉`AddMsgTemplate/util.js`与`Tinymce/util.js`
 */
let verificationCodeTemplate;
verificationCodeTemplate = `
  <table border="0" width="750" align="center" cellpadding="0" cellspacing="0">
        <tr>
          <td style="font-size:0px;width:750px;height:44px;"></td>
        </tr>
        <tr>
          <td style="font-size:0px;width:750px;height:316px;">
            <table align="center" border="0" cellpadding="0" cellspacing="0" width="662"
              style="border-collapse:collapse;font-size:0px">
              <tr>
                <td
                  style="font-size:0px;width:662px;height:44px;background:linear-gradient(90deg, #FDFEFF 0%, #FCFAF7 51%, #FBF6F3 100%);">
                  <img width="111" height="37"
                    src="http://life-content.veryfitplus.com/device/369898dd-7c0b-43cd-ae8b-6a227ce15837.png" />
                </td>
              </tr>
              <tr>
                <td style="font-size:0px;height:26px;"></td>
              </tr>
              <tr>
                <td style="font-weight: bold;font-weight:600;font-size:16px;line-height:2;">
                  尊敬的用户：
                </td>
              </tr>
              <tr>
                <td style="font-size:16px;line-height:2;text-indent:25px;">请验证您的Veryfit账户ID
                  「\${account}」，如果您输入以下验证码，Veryfit账户将完成认证。
                  该验证码十分钟内有效，请不要转给其他人。</td>
              </tr>
              <tr>
                <td style="font-size:0px;height:26px;"></td>
              </tr>
              <tr>
                <td style="font-size:0px;width:662px;height:60px;">
                  <table align="left" border="0" cellpadding="0" cellspacing="0" height="60" style="background:#FAFBFD;border-radius:4px;
      border:1px dashed #E0E4E9;">
                    <tr>
                      <td style="font-size:0px;" width="24"></td>
                      <td style="color:#91959B;min-width: 44px;font-size:16px;white-space: nowrap;">验证码：</td>
                      <td
                        style="color:#FF4A00;font-size:26px;font-weight:600;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;">
                        \${code}</td>
                      <td style="font-size:0px;" width="24"></td>
                    </tr>
                  </table>
                </td>
              </tr>
              <tr>
                <td style="font-size:0px;height:35px;"></td>
              </tr>
              <tr>
                <td style="font-size:16px;line-height:2;">
                  Veryfit团队敬上
                </td>
              </tr>
              <tr>
                <td style="font-size:0px;height:36px;"></td>
              </tr>
              <tr>
                <td style="font-size:16px;line-height:2;">
                  运动更健康
                </td>
              </tr>
            </table>
          </td>
        </tr>
        <tr>
          <td style="font-size:0px;width:750px;height:44px;"></td>
        </tr>
      </table>
`;

// 自定义的按钮
export function myButton(editor) {
  // 帮助按钮
  editor.ui.registry.addButton("helpToolTip", {
    text: "帮助",
    onAction: function (_) {
      const h = vm.$createElement;
      vm.$msgbox({
        title: "帮助",
        message: h(
          "div",
          { style: "line-height: 32Px;font-size: 16Px;" },
          [
            h(
              "li",
              null,
              "图片不与文字共一行, 图片应独立占一行, 上传图片前注意要换行"
            ),
            h("li", null, "多余的空行注意删掉"),
            h(
              "li",
              null,
              `选择"工具栏"中的"段落样式"可以为文本添加预设样式. 操作方式为：选择一个段落 —— 点击"工具栏"的"段落样式" —— 选择其中某一项样式`
            ),
            h(
              "li",
              null,
              `建议在word文档处理完所有格式后, 再粘贴进富文本编辑器内`
            ),
            h("li", null, `不建议在富文本编辑器内使用超链接`),
          ]
        ),
        showCancelButton: false,
        confirmButtonText: "确定",
      })
    },
  });

  // 预定义的模板
  let toggleState = false;
  editor.ui.registry.addMenuButton('presuppositionTemplate', {
    text: '预设模板',
    fetch: function (callback) {
      let items = [
        {
          type: 'menuitem',
          text: '短信验证',
          onAction: function () {
            editor.insertContent(verificationCodeTemplate);
          }
        },
        {
          type: 'nestedmenuitem',
          text: '其他',
          getSubmenuItems: function () {
            return [
              {
                type: 'menuitem',
                text: '未定义',
                onAction: function () {
                  editor.insertContent('&nbsp;<em>未定义</em>');
                }
              }
            ];
          }
        },
      ];
      callback(items);
    }
  });
}
```
### 在同一页面不能显示多个Tinymce编辑器的问题
- 需要配置属性`selector`,需要不同的`id`
- 示例
  ```js
  data() {
    return {
      init: {
        selector: "#" + this.selectorId,
      }
    }
  }
  ```

## 参考
- [在 Vue 项目中引入 tinymce 富文本编辑器](https://www.cnblogs.com/wisewrong/p/8985471.html)
- [tinymce 5更新后，如何使用tinymce-vue](https://segmentfault.com/a/1190000018358304?utm_source=tag-newest)
- [官方文档](https://www.tiny.cloud/docs/configure/editor-appearance/#resize)
- [tinymce自定义按钮](https://blog.csdn.net/zjiang1994/article/details/79880806?utm_source=blogxgwz8)
  
