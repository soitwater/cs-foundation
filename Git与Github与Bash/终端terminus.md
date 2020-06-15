# 终端terminus
- 这是Electron应用
- 内存占用`400MB`起步(远高于`git for window`与`powerShell`)  

## 安装
* 下载git for windows并安装:https://gitforwindows.org/
* 下载terminus并安装, 官方下载地址 Terminus(portable.zip版是win10的):  https://github.com/Eugeny/terminus/releases/tag/v1.0.112
* 设置, 单击左上角的齿轮  
* shell设置页, shell选择为Git-Bash  
* terminal设置页, 右键设置为粘贴, 勾选`软件打开时自动开启一个终端`,  勾选`阻止自动执行复制的到终端的内容`, 勾选`选择时自动复制`
* hotkeys设置页, 增加ctrl+v为粘贴快捷键. 这样, 最大的复合win的复制粘贴快捷键, 还是mac系统更合理, cmd+c是复制, cmd+v是粘贴. 与terminal的标准中断快捷键ctrl+c没有冲突  
* 新建~/.bashrc文件, 并加入下面的内容, 
  ```shell
  # 按向上向下键自动匹配相应前缀的历史命令
  bind '"\e[A": history-search-backward' 
  bind '"\e[B": history-search-forward' 
  bind '"\e[C": forward-char' 
  bind '"\e[D": backward-char'
  # 最大可能兼容win和linux的命令, 注意等号前后不能有空格
  alias ifconfig='ipconfig' 
  alias ll='ls -lah'
  ```
  保存后, 重新打开terminus或者执行source ~/.bashrc即可
- 设置—— Application —— Acrylic background 勾选 —— 并把 Background type 改为 Fluent