# VSCode
- windows配置文件目录`C:\Users\CSY\AppData\Roaming\Code\User`
- [VS Code使用之基本设置与配置详解](https://www.jianshu.com/p/e7d67cfd5893)
- [2018 vscode 前端最佳配置](https://blog.csdn.net/win7583362/article/details/79315055/)

## 推荐的插件
- import cost (显示导入的包的大小)
- GitLens
- Chinese (Simplified) Language Pack for Visual Studio Code
- Code Runner (不用再`node a.js`了)
- Markdown All in One
- WakaTime (统计过去七天的写码时间)
- Bracket Pair Colorizer 用不同颜色高亮显示匹配的括号
- Version Lens 工具包最新版本信息
- vscode-icons 改变文件夹的icons
### cpp插件
- cmake cpp的多文件编译
- C/C++
- C++ Intellisense 智能提示
- CMake Tools 扩展CMake
  

## 个人配置(eslint 换行符)
```json
{
    "eslint.autoFixOnSave": true,
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "html",
        {
            "language": "html",
            "autoFix": true
        },
        {
            "language": "vue",
            "autoFix": true
        }
    ],
    "eslint.options": {
        "plugins": [
            "vue"
        ]
    },
    "files.eol": "\n",
    "jetbrainsKeymap.promptV3Features": true,
    "editor.multiCursorModifier": "ctrlCmd",
    "editor.formatOnPaste": true,
    "editor.tabSize": 2,
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "breadcrumbs.enabled": true,
    "git.confirmSync": false,
    "javascript.updateImportsOnFileMove.enabled": "always",
    "[json]": {
        "editor.defaultFormatter": "vscode.json-language-features"
    },

    "window.zoomLevel": -1,
    "explorer.confirmDelete": false,
    "git.enabled": false,
    "editor.suggestSelection": "first",
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "workbench.sideBar.location": "left",
    // 终端样式
    "terminal.integrated.cursorBlinking": true,
    "terminal.integrated.lineHeight": 1.2,
    "terminal.integrated.letterSpacing": 0.1,
    "terminal.integrated.fontSize": 15, 
    "terminal.integrated.fontFamily": "Lucida Console", 
    "terminal.integrated.shell.windows": "C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\powershell.exe", // 使用 powershell(ctrl+shift+p,输入shell,选择,可以选择其他shell)
}
```

## 快捷键
- `Alt + 向上箭头` / `Alt + 向下箭头` 当前行向上/向下移动
- `Ctrl + Shift + T ` 重新打开关闭的标签页 
- `Ctrl + Backspace` 删除上一个单词
- `Ctrl + Shift +右箭头` / `Ctrl + Shift +左箭头` 逐个选择文本
- `Ctrl + Home` / `Ctrl + End` 移至文件的开头/结尾
- 选中文本后按`Ctrl + F2`, 若该文本在当前文件中出现多个,那么则全部选中
- 快速折叠代码:先`Ctrl + k` 再 `Ctrl + 0`
## 其他设置
- `chcp 65001`设置控制台编码为`utf8`
- `chcp 936`设置控制台编码为`gbk2312`(简体中文)