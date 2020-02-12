# \<html/\>与\<head/\>

## `<head/>`
- `lang='zh-cmn-Hans'`  
  `<html lang="zh-cmn-Hans"/>`，表示`<html/>`元素下所有元素都是简体中文  
  “zh-cmn-Hans”表示“中文(大类) - 普通话 - 简体格式”，这是为了兼容不同的浏览器

## `<head/>`
- `<title></title>`
- `<meta>`
  * 移动端完整版`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`
  * 不用关闭, 作用是对DOM进行描述，便于SEO
  * 模板：该标签以键值对的形式书写，`<meta name="关键词" content="值">`
  * 关键词有2类，`name`以及`http-equiv`
  * 功能：
    1. 定义所使用的语言 `<meta charset="utf-8">`
    2. SEO相关
       - 告诉搜索引擎网页关键词 `<meta name="keywords" content="购物,电商">`
       - 告诉搜索引擎网页功能 `<meta name="description" content="购物网站,电子商务,">`
       - 标注网站的作者 `<meta name="author" content="root,123@qq.com">`
    3. 浏览器渲染相关
       - 渲染模式 `<meta name="renderer" content="webkit">`
       - 视图模式 `<meta name="viewport" content="width=device-width,initial-scale=1">`
    4. http-equiv(用于在发送HTML文件到浏览器前通知浏览器一些信息,以便于浏览器做好准备)
       - 浏览模式 `<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">`
       - 网页到期时间(必须GMT格式时间,过期必须到服务器重新传输) `<meta http-equiv="expires" content="Sat May 25 2019 12:17:43 GMT+0800 (中国标准时间)">`
       - 禁用缓存访问(可设置网页无法脱机访问) `<meta http-equiv="Pragma" content="no-cache">`
       - 自动刷新(停留2秒后自动刷新并跳转到新页面) `<meta http-equiv="Refresh" content="2;URL=http://jandan.net/ooxx">`
       - Set-Cookie(如果网页过期,自动删除Cookie)`<meta http-equiv="Set-Cookie"content="cookievalue=xxx;expires=Friday,12-Jan-200118:18:18GMT；path=/">`
       - 强制窗口以独立页面显示,防止别人在框架内调用该网页 `<meta http-equiv="Window-target" content="_blank">`

## 参考
- [网页头部的声明应该是用 lang="zh" 还是 lang="zh-cn"？](https://www.zhihu.com/question/20797118)
- [Meta标签用法大全](https://www.cnblogs.com/qiumohanyu/p/5431859.html)
- [HTML\<meta\>标签](https://www.cnblogs.com/xzhang/p/3977025.html)