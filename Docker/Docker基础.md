# Docker基础

## Docker与传统虚拟机对比
- 传统虚拟机还模拟硬件（内存、CPU），启动慢，占用多
- Docker不模拟硬件，无内核，启动快

## 安装
### 网站
- 一般参考：https://www.cnblogs.com/linjj/p/5606833.html
- 非常推荐参考： https://www.cnblogs.com/ginponson/p/8601320.html
- win10 官方安装方法：https://www.runoob.com/docker/windows-docker-install.html  菜鸟教程
- win10安装需要非家庭版，当然家庭版也有绕过的方法  https://blog.csdn.net/hunan961/article/details/79484098
- Git，本来已安装的话也不用安装
- Kitematic，docker的界面化，没什么太大需要
- 需要开启  cpu -- 虚拟化：magicbook 开机时 不停按 F2，进入后选择 中文，找到虚拟技术

### 注意
1. 以管理员身份运行
2. 右键图标“Docker Quickstart Terminal”——“属性”："E:\Git\bin\bash.exe" --login -i "D:\docker\Docker Toolbox\start.sh" 注意这两个文件的格式正确
3. 报错：Looks like something went wrong in step Looking for vboxmanage.exe… Press any key to continue… 
   - 是系统变量没有配全：DOCKER_MACHINE="${DOCKER_TOOLBOX_INSTALL_PATH}\docker-machine.exe" 改成 DOCKER_MACHINE="D:\docker\Docker Toolbox\docker-machine.exe" 绝对路径
   - ${VBOX_MSI_INSTALL_PATH} 或者 ${VBOX_INSTALL_PATH} 看系统变量是否二者之一有
4. 将虚拟机安装在系统盘可能会带来各种问题，所以趁docker的镜像还没膨胀的时候赶快迁移吧。下面步骤可以在安装前做(打开docker 终端之前)，这样可以减少删除已安装的default docker虚拟机。
   - 在Windows的系统环境添加MACHINE_STORAGE_PATH ，指向虚拟机的位置（我推荐D:\VM\machines）
   - 复制boot2docker.iso到虚拟机的位置同级目录cache上（D:\VM\cache），如果不是最新的话需要下载最新。
   - 下面分两种情况：
     * 如果还没安装default docker虚拟机，可以进入Docker QuickStart直接安装。
     * 已安装了default docker虚拟机，需要先删除原来的，再安装到指定位置。
5. docker安装卡死在boot2docker.iso的下载
   * 到这个网站  https://github.com/boot2docker/boot2docker/releases/  下载提示的版本，然后放在提示的 /Users/apple/.docker/machine/cache/目录中，重启 docker
   * 原因：地址和github是不对应的，因此会出现现在失败。
   * 或者参考 https://blog.csdn.net/dhaiuda/article/details/81975550  ，直接复制 Docker Toolbox文件夹下的boot2docker.iso ，断网，重启docker
6. 常见错误一览：https://www.daixiaorui.com/read/277.html
7. 默认ip 192.168.99.100
8. 测试是否安装成功：docker  一堆字符即成功

## 命令
| 命令                                              | 功能                   | 备注                                                                                                      |
| ------------------------------------------------- | ---------------------- | --------------------------------------------------------------------------------------------------------- |
| docker images                                     | 本地拥有的镜像         | -                                                                                                         |
| docker ps -a                                      | 查看容器               | 若为 docker ps 则为查看运行中的容器                                                                       |
| docker inspect  [ 容器运行成功后返回的id的前2位 ] | 查看容器信息           | -                                                                                                         |
| docker stats  [ 容器运行成功后返回的id的前2位 ]   | 查看docker状态         | 会不断查找, 如何退出?                                                                                     |
| docker exec -it [容器id前2位或容器名]  /bin/bash  | 进入指定容器           | 注意 /bin 前空格                                                                                          |
| docker commit [容器id] [新镜像名称]               | 将容器打包成镜像       | -                                                                                                         |
| docker export -o [导出名称.tar] [容器名or容器ID]  | 保存容器快照           | [参考](https://blog.csdn.net/liukuan73/article/details/78089138)	生成的文件位置  D:\docker\Docker Toolbox |
| docker import [快照名.tar] 新镜像名               | 将快照恢复为image      | -                                                                                                         |
| docker rm -f [容器id]                             | -                      | 可以使用"docker rm 容器id"来删除一个终止状态的容器；若要删除一个运行中的容器，需要加-f参数。              |
| docker start [容器id]                             | 进入容器前需要启动容器 | -                                                                                                         |
| docker attach [容器id]                            | 进入容器               | -                                                                                                         |
| docker rmi [镜像id]                               | 删除镜像               | -                                                                                                         |

## 镜像
- 在阿里云申请创建镜像仓库
  * 点击左侧的“镜像加速器”，选择操作系统标签，
- 阿里云镜像加速方法   https://cr.console.aliyun.com/cn-shenzhen/instances/mirrors
- 使用 Docker Toolbox 的用户
  * 运行docker-machine stop default，停止运行中的虚拟机
  * 运行docker-machine rm default，删除默认的虚拟机
- 断网
  ```
  在 D:\docker\vm\cache 路径下 放一份 boot2docker.iso  （注意版本）
  docker-machine create --engine-registry-mirror=https://ew1d899t.mirror.aliyuncs.com -d virtualbox default
  docker-machine env default
  eval "$(docker-machine env default)"
  docker info
  ```
- 创建一个前端镜像
  * 参考：https://www.jianshu.com/p/04a34cbfcdfb
  * 拉取 ubuntu 
  * docker search ubuntu:14.04
  * docker pull ubuntu:14.04
  * docker images 
- 运行docker镜像：
  * docker run -it -d --name ubuntu_test -p 8088:80 ubuntu
  * -d参数为后台运行，--name参数为自定义容器名，-p参数为指定端口映射、后者为容器的端口
  * 运行成功后将返回一个容器id，记住前两位即可
- docker ps  查看容器
- docker exec -it ubuntu_test /bin/bash  进入指定容器
- 换源:  参考：https://blog.csdn.net/ski_12/article/details/76696436
  * cat /etc/issue  查看ubuntu 版本
  * 备份原来的源
  * cd /etc/apt/  
  * ls
  * cp sources.list sources.list.bak
  * ls
  * 先安装更新源以便于下载 vim 再修改文件
  * apt-get update
  * apt-get install vim
  * 将源文件置空：echo ‘’ > sources.list （这里是2个单引号）
  * vim sources.list
  * 对于 ubuntu 18.04，粘贴一下内容
  * deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
  * deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
  * deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
  * deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
  * deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
  * deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
  * deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
  * deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
  * deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
  * deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
- 保存并退出 vim：按住 Esc情况下，shift + z + z
  * apt-get update
  * apt-get upgrade
- 安装 （速度明显变快）
  * （直接安装nodejs 是否可以附带安装 npm）apt-get install npm
  * 淘宝镜像 npm install -g cnpm --registry=https://registry.npm.taobao.org
  * 或直接设置   npm config set registry https://registry.npm.taobao.org
  * 安装nodeJS   apt-get install nodejs
  * npm install n -g     升级nodejs以及npm版本
  * npm install npm@latest -g  安装最新的npm
  * n stable  安装最新的稳定版本nodejs
  * 更新后需要退出，不然后node以及npm的版本还是老样子
- 退出 ubuntu 方式： ctrl + p, 再 ctrl + q
- docker commit -t  registry:tag devtest3 
