# MySQL

## 启动
- 在运行输入services.msc，查找是否有你的MySQL服务器的名字；若未启动，则右键/ 启动
- 连接：需要账号，密码
- 新建数据库：字符集：utf8 -- UTF-8 Unicode，排序规则：utf8_general_ci
- 新建表
- 新建字段
- 新建查询：点击Navicat 顶部工具栏的“查询”，再点击“新建查询”

## MYSQL的目录结构
- bin目录：用于存储一些可执行文件，如mysql.exe
- include目录：用于存储包含的一些头文件，如mysql.h等
- lib目录：用于存储一些库文件
- share目录：用于存储错误信息、字符集文件等
- data目录：用于放置一些日志文件以及数据库
- my.ini文件：数据库的配置文件

## DBMS大致分2类
1. 基于共享文件系统的DBMS(Microsoft Access)
2. 基于客户机-服务器的DBMS(MySQL)
   * 即用户使用客户机(软件)提出需求，需求上传到服务器，
   * 再由服务器软件访问数据仓库完成操作，最后将结果返回客户机软件。
   * 因此MySQL的连接需要设置账号，密码，端口

## SQL
### 选择数据库
- 关键字: USE
- 使用USE打开数据库，才能读取其中的数据
- 不能用关键字命名一个表或列
### SHOW DATABASES;
- 返回可用的数据库
### 返回某一个数据库的视图
```sql
USE 数据库名;
SHOW TABLES;
```
### 显示表的字段信息;
```sql
SHOW COLUMNS FROM 表名;
# 该语句等价于
DESCRIBE 表名
```
## 显示表的其他信息
- SHOW STATUS; 用于显示广泛的服务器状态信息
- SHOW GRANTS; 显示授予用户(所有用户或特定用户)的安全权限
- SHOW ERRORS; 显示服务器的错误
- SHOW WARNINGS; 显示服务器的警告



## 参考
- 《MySQL 必知必会》