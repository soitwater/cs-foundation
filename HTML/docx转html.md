# docx转html
- mammoth 
  * 缺点：word文档的居中无效，word文档的样式无效

## 使用
- 安装：  cnpm i mammoth -S
- 在html文件中
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>docx转化为html</title>
  </head>

  <body>
    <input type="file" id="input-file" />
    <script>
    </script>
  </body>

  </html>
  ```
- 在`*.js`文件中
  ```js
  import mammoth from 'mammoth/mammoth.browser.js';

  const inputEl = document.getElementById('input-file');
  inputEl.addEventListener('change', function (evt) {
    readFileInputEventAsArrayBuffer(evt);
  });

  /*
    * 将上传的 *.docx 文件转化为 *.html 
  */
  function readFileInputEventAsArrayBuffer(event) {
    const file = event.target.files[0];
    console.log('获取到的文件', file);
    console.log('mammoth.convertToHtml', mammoth.convertToHtml)
    const reader = new FileReader();
    reader.onload = function (loadEvent) {
      const arrayBuffer = loadEvent.target["result"];
      // 修改 *.docx 遇见 image 后的回调 
      const convertImage = mammoth.images.imgElement(function(image) {
        return image.read("base64").then(async (imageBuffer) => {
          const result = await 图片上传api(imageBuffer, image.contentType);
          return {
            src: result.data.path // 获取图片线上的URL地址
          };
        });
      });

      mammoth.convertToHtml({ arrayBuffer }, { convertImage })
        .then(function(result) {
          const html = result.value;
          const msg = result.messages;
          console.log('获得的', result)
        })
        .done();
    };
    reader.readAsArrayBuffer(file);
  }
  ```

## 参考说明
- [官方文档](https://www.npmjs.com/package/mammoth)
- [官方文档2](https://github.com/mwilliamson/mammoth.js)
- [教程](https://segmentfault.com/a/1190000021111457)

