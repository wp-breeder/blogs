## docker 基础教程
`2017-04-28`
[TOC]
###  一、docker 安装
具体安装方法见[官方文档](https://docs.docker.com/engine/installation/)
>作者注: 对容器与镜像的理解，容器就像虚拟机里实例，镜像就像快照。

### 二、docker 常用命令
#### `images` 查看所有本地镜像
```shell
example:
sudo docker images
#输出
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              f7b3f317ec73        3 days ago          117 MB
```
#### `ps [-a]` 查看正在运行中(所有)容器
```shell
docker ps [-a]
example:
    sudo docker ps -a
    #输出
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
    0c3d1146abd9        ubuntu              "/bin/bash"         30 minutes ago      Exited (0) 24 minutes ago                       php7
```
#### `run` 创建并运行一个新容器
```shell
sudo docker run --name php7 -itd ubuntu
#生成一个正在运行中的容器
sudo docker ps
#输出
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
0c3d1146abd9        ubuntu              "/bin/bash"         35 minutes ago      Up 2 seconds                            php7
#常用参数说明
--name 指定新容器的名称;
-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项;
-d: 后台运行容器，并返回容器ID;
-i: 以交互模式运行容器，通常与 -t 同时使用;
-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用;
--name="nginx-lb": 为容器指定一个名称;
--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致;
--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致
-h "mars": 指定容器的hostname;
-e username="ritchie": 设置环境变量;
--env-file=[]: 从指定文件读入环境变量;
--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行;
-m :设置容器使用内存最大值;
--net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型;
--link=[]: 添加链接到另一个容器;
--expose=[]: 开放一个端口或一组端口;
-p 一个容器的端口映射到宿主机的端口 example: sudo docker run -p 80:8080 -d ubuntu 把容器的80端口映射到8080
-v 把容器的目录挂载到宿主机的目录 example: sudo docker run -v /var/www/data:/home/data -d ubuntu 把容器的/var/www/data 挂载到 主机的/home/data 
```
#### `attach` 进入一个后台运行中的容器
```shell
docker attach [容器ID|容器name]
example:
    sudo docker attach php7
    #进入正在运行的php7容器
    root@0c3d1146abd9:/#
```
#### 正常退出一个容器
```shell
#不关闭容器，让容器在后台继续运行
Ctrl-P + Ctrl-Q
#退出并关闭容器
exit
```
#### `search` 在docker hub 上搜索镜像
```shell
docker search [OPTIONS] TERM
example:
    sudo docker search ubuntu 
    #输出
    NAME                                         DESCRIPTION                                        STARS     OFFICIAL   AUTOMATED
    ubuntu                                       Ubuntu is a    Debian-based Linux operating s...   5919      [OK]
    rastasheep/ubuntu-sshd                       Dockerized SSH service, built on top of of...   81                   [OK]
    ...
```
#### `pull` 从远程仓库拉取镜像
```shell
 docker pull [OPTIONS] NAME[:TAG|@DIGEST]
 example:
     sudo docker pull ubuntu
     #输出
     Using default tag: latest
```
#### `commit` 基于某个容器修改之后，保存当前状态
```shell
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
    example:
    sudo docker commit -m='A new image' --author="breeder" php7 breeder/php7
    #输出 sha256代表成功
    sha256:b42d2e034cd74c6cf0fee687af9a8f0463fcc9d26ff6452f8ccb06ac52ba9760
    #查看是否保存镜像成功
    sudo docker images
    #输出
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    breeder/php7        latest              b42d2e034cd7        5 seconds ago       117 MB
    ubuntu              latest              f7b3f317ec73        3 days ago          117 MB
```
#### `inspect` 查看镜像详细信息
```shell
sudo docker inspect [容器ID|容器名                                                                                                                                                                                                                                                                                                                                                                       称]
```
#### `start | restart | stop` 启动/重启/停止容器
```shell
docker start|restart|stop [OPTIONS] CONTAINER [CONTAINER...]
example:
    sudo docker start php7
    #输出
    php7
```
### 三、Dockerfile
Dockerfile的[使用方法](http://blog.csdn.net/wsscy2004/article/details/25878223)
                                       