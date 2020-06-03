# Redis

## 基础
- CAP理论：在一个分布式系统中，一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）
- Key-Value Store的优势：
  * 可扩展性：加机器即可获得更大的数据存储
  * 并发性：关系型数据库几百并发就很吃力，Key-Value Store几千并发轻轻松松
- 数据类型：数字 / 字符串 / Lists 列表 / Sets 集合 / Sorted sets 有序集合 / Hashes 哈希表
- 应用场景：取最新N个数据，取Top N个数据，实时消息系统，缓存

## 安装
- 下载地址`https://github.com/microsoftarchive/redis/releases`
- 解压

## 启动
- （命令行）启动临时服务`./redis-server.exe  ./redis.windows.conf`(不会在window的服务列表中出现)
- 双击`redis-cli.exe`,出现窗口,在窗口内可执行数据库操作
  
## 基本使用
### redis.windows.conf修改配置
- `port 6379` change to `port 6378`
- 数据库密码`requirepass 123456`（登录时输入`auth 123456`）
- 设置`key`--`set keyA 111`;获取`key`--`get keyA`
- `KEYs *`查找所有`key`
- `DEL keyA`删除`key`
- 在nodejs中使用`redis`:`cnpm i -D ioredis`,address:`https://www.npmjs.com/package/ioredis`



## 参考
- [《Redis实战》]()
- [redis安装](https://blog.csdn.net/yangwenxue1989/article/details/88884668)