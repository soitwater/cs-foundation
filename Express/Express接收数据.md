# Express接收数据

## 前端发送数据的方式
- [HTTP请求中的form data,request payload,query string parameters以及在node服务器中如何接收这些参数](https://www.cnblogs.com/hsp-blog/p/5919877.html)
- Form Data	
  直接`req.body`
- Request Payload	  
  以流的方式接收数据
  ```js
  var str="";
  req.on("data",function (chunk) { 
    str+=chunk
  })
  ```
- query string parameters  
  出现在Get方法，直接`req.body`