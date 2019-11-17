# gh-pages
用于为仓库添加在线演示Demo

## 步骤
- 前提是账号上已经有个人仓库：\[账户ID\].github.io
- 上传代码到“master”分支
- 新建并切换到“gh-pages”分支：`git checkout -b gh-pages`
- 将`master`分支下的内容推送到`gh-pages`分支下：`git push origin master:gh-pages`
  * 其实在 github 页面上新建 默认分支时，`master`分支下的内容已经默认推送到`gh-pages`分支下
- 假如`index.html`以及依赖的JS文件放在WebDemo仓库下的/Web/dist文件夹里，那么访问链接：  
  `https://[github账户ID].github.io/WebDemo/Web/dist)`
- 补充github分支推送命令：  
  ```
  在新分支下
  git add .
  git commit -m "略"
  git push origin 新分支名
  ```

## 参考
- [如何用Github的gh-pages分支展示自己的项目](https://www.cnblogs.com/MuYunyun/p/6082359.html)