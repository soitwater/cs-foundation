# VSCode
- windows配置文件目录`C:\Users\CSY\AppData\Roaming\Code\User`
- [VS Code使用之基本设置与配置详解](https://www.jianshu.com/p/e7d67cfd5893)
- [2018 vscode 前端最佳配置](https://blog.csdn.net/win7583362/article/details/79315055/)

## 推荐的插件
- import cost
- GitLens
- 

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