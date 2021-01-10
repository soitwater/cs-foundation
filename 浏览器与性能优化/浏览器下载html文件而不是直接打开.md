# 浏览器下载html文件而不是直接打开

## 在`util.js`
```js
/**
 *下载或导出文件
 * @param blob  ：返回数据的blob对象
 * @param tagFileName  ：下载后文件名标记
 * @param fileType  ：文件类 word(docx) excel(xlsx) ppt等
 */
export function saveFileToBlob(blob, tagFileName, fileType) {
  if (fileType) {
    console.warn('未传入文件类型,例如文件类 word(docx) excel(xlsx) ppt jpg png等');
  }
  if (isBlob(blob)) {
    var downloadElement = document.createElement('a');
    var href = window.URL.createObjectURL(blob); //创建下载的链接
    downloadElement.href = href;
    downloadElement.download = (tagFileName ? tagFileName : getTimestamp()) + '.' + fileType; //下载后文件名
    document.body.appendChild(downloadElement);
    downloadElement.click(); //点击下载
    document.body.removeChild(downloadElement); //下载完成移除元素
    window.URL.revokeObjectURL(href); //释放掉blob对象
  } else {
    console.warn('不是blob对象类型的参数,可选择saveFileToBase64(Base64对象,文件类型)或saveFileToLink(文件链接,文件名,文件类型,进度回调方法)');
  }
}

function getTimestamp() {
  let time = new Date();
  return ('' + time.getFullYear()) + formatTime(time.getMonth() + 1) + formatTime(time.getDate()) + formatTime(time.getHours()) + formatTime(time.getMinutes()) + formatTime(time.getSeconds());
  function formatTime(t) {
    return t < 10 ? '0' + t : t
  }
};

function isBlob(x) {
  return x instanceof Blob || toString.call(x) === '[object Blob]';
};
```

## 点击事件
```js
handleClick() {
  let _this = this;
  let xhr = new XMLHttpRequest();
  xhr.open("get", this.url, true); // true（异步）或 false（同步）
  xhr.setRequestHeader(
    "Content-Type",
    this.filetype == "html" ? `text/html` : "application/json"
  );
  xhr.responseType = "blob"; // 这儿需要blob对象
  xhr.onload = function () {
    if (this.status == 200) {
      let blob = this.response;
      let filetype = 'html';
      try {
        filetype = _this.filetype;
      } catch (err) {
        console.error('1148', err);
      }
      saveFileToBlob(blob, getName(), filetype);
      _this.$notify({
        title: "温馨提示",
        message: "模板已开始下载",
        type: "info",
        customClass: "my-el-notify",
      });
    }
  };
  xhr.send();
  window.open(this.url);

  function getName() {
    let str = (+new Date()) + '';
    try {
      str = _this.url;
    } catch (err) {
      console.log('1144\n', err);
    }
    let result = "";
    try {
      result = str.substring(str.lastIndexOf("/") + 1, str.lastIndexOf("."));
    } catch (err) {
      console.log("1515 获取链接名错误");
    } finally {
      return result;
    }
  }
},
```
