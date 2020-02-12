# fetch

- fetch是基于Promise设计，在特旧的浏览器上是不支持的
- fetch为浏览器原生api,但IE以及大部分移动端浏览器均不支持,需要使用`fetch polyfill`
- 普通的Ajax都非常粗糙,且含回调函数语法,非常不友好
- axios也是对xhr的封装，且支持Promise

## 使用
上传JSON例子
```js
function postData(url, data) {
  // Default options are marked with *
  return fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: new Headers({
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    }),
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}
```
上传文件例子
```js
var formData = new FormData();
var fileField = document.querySelector("input[type='file']");

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData // 虽然没有设置 content-type, 但是fetch会试着自己设置
})
.then(response => response.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
```

options支持的配置  
- method(String): HTTP请求方法，默认为GET
- body(String): HTTP的请求参数
- headers(Object): HTTP的请求头，默认为{}
- credentials(String): 默认为omit,忽略的意思，也就是不带cookie;
  * same-origin，意思就是同源请求带cookie；
  * include,表示无论跨域还是同源请求都会带cookie

## 检测请求是否成功
fetch即使接收到响应状态码`4xx`或`5xx`也不会触发`reject`  
只有在遇到网络故障等情况时,fetch才会触发`reject`  
想要判断请求是否成功，需要在`resolve()`中判断`response.ok`
```js
fetch('flowers.jpg').then(function(response) {
  if(response.ok) {
    return response.blob();
  }
  throw new Error('Network response was not ok.');
}).then(function(myBlob) { 
  var objectURL = URL.createObjectURL(myBlob); 
  myImage.src = objectURL; 
}).catch(function(error) {
  console.log('There has been a problem with your fetch operation: ', error.message);
});
```

## Response 对象
即响应。
- Response.status — 整数(默认值为200) 为response的状态码.
- Response.statusText — 字符串(默认值为"OK"),该值与HTTP状态码消息对应.
- Response.ok  - 布尔值，表示状态吗是否`2xx`

## Body
配置项中`body`支持的文件类型以及获取其中文件的方式
- arrayBuffer()
- blob()
- json()
- text()
- formData()
```js
fetch(myRequest).then(function(response) {
  if(response.headers.get("content-type") === "application/json") {
    return response.json().then(function(json) {
      // process your JSON further
    });
  } else {
    console.log("Oops, we haven't got JSON!");
  }
});
```

## 参考
- [MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch#Body)