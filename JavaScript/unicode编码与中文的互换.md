# unicode编码与中文的互换

## unicode编码转成中文
```js
var str = "\\u6211\\u662Funicode\\u7F16\\u7801";

function unicode2Chinese(str) {
  var temp = unescape(str.replace(/\\u/g, "%u"));
  return temp;
}

console.log(unicode2Chinese(str));
```

## 中文转成unicode编码
```js
function unicode(str) {
  var value='';
  for (var i = 0; i < str.length; i++) {
    value += '\\u' + left_zero_4(parseInt(str.charCodeAt(i)).toString(16));
  }
  return value;

  function left_zero_4(str) {
    if (str != null && str != '' && str != 'undefined') {
      if (str.length == 2) {
        return '00' + str;
      }
    }
    return str;
  }
}

console.log(unicode("我是"));
```