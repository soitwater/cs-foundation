# nrm镜像管理
- 用于配置`npm`的源

## 命令
```c
npm install -g nrm

// 查看可选的源
nrm ls

// 切换源
nrm use taobao

// 新增定制的源,如公司私服
nrm add <registry-name> <url>

// 删除指定的源
nrm del <registry-name>

// 测试源的速度
nrm test npm             
```