### 一、编译安装redis
----
#### 1. 下载 redis
##### 1. 进入编译目录，这里我选择的`/opt`目录。
```shell
sudo chmod 777 /opt
cd /opt
sudo wget http://download.redis.io/releases/redis-3.2.2.tar.gz
```
	
##### 2. 下载完成后，解压`redis`。
```shell
sudo tar -zvxf redis-3.2.2.tar.gz
```
	
##### 3. 因为redis本身就是makefile,所以编译起来非常简单，直接进入源码目录，make即可。
```shell
cd redis-3.2.2/
sudo make
sudo make PREFIX=/usr/local/redis-3.2.2 install
sudo cp redis.conf /usr/local/redis-3.2.2/
```
到此时，`redis` 已经安装完成了，接下来配置 `redis`。

#### 2.配置redis
##### 1. 复制配置文件到`/etc/redis`
```shell
sudo mkdir -p /etc/redis/
sudo cp /opt/redis-3.2.2/redis.conf /etc/redis/6739.conf
```
##### 2. 修改配置
```shell
sudo vi /etc/redis/6739.conf
#把 daemonize no 改为 aemonize yes，保存并退出
```
##### 3. 复制安装包里的redis_init_script到/etc/init.d/,并赋权
```shell
sudo cp /opt/redis-3.2.2/utils/redis_init_script /etc/init.d/redis
sudo chmod 777 /etc/init.d/redis
```
##### 4. 配置redis启动脚本
```shell
sudo vi /etc/init.d/redis
    #配置端口 默认是6379
    REDISPORT=6379
    #redis server绝对路径
    EXEC=/usr/local/redis-3.2.2/bin/redis-server
    #redis 客户端绝对路径
    CLIEXEC=/usr/local/redis-3.2.2/bin/redis-cli
    #redis pid 绝对路径，默认redis_端口号.pid
    PIDFILE=/var/run/redis_${REDISPORT}.pid
    #redis 配置文件绝对路径，默认端口号.conf
    CONF="/etc/redis/${REDISPORT}.conf"
```
##### 5. 把`redis`注册成为系统服务
```shell
#ubuntu 注册服务 15.04用systemd管理
sudo update-rc.d redis defaults
#centos 添加开机自启 7以上用systemd管理
sudo chkconfig redis on
```
##### 6. `redis`服务开启命令为
```shell
sudo service redis start
#或
sudo /etc/init.d/redis start
```
##### 7. 查看`redis`服务是否正常启动
```shell
 ps -aux |grep redis
```
##### 8. 关闭`redis`的命令为
```shell
sudo service redis stop
#或
sudo /etc/init.d/redis stop
#或
sudo redis-cli shutdown
```
##### 9. 进入`redis`客户端的命令为
```shell
redis-cli
```
### 二、编译安装 phpredis-php7 扩展
##### 1. 下载 `phpredis` 扩展源码
```shell
cd /opt
wget -c https://github.com/phpredis/phpredis/archive/php7.zip
```
##### 2. 解压 `php7.zip`
```shell
sudo apt-get instarll zip
#安装完成后解压 `php7.zip`
unzip php7.zip
#解压完成后，进入 `phpredis-php7` 目录
cd phpredis-php7/
```
##### 3. 开始编译phpredis扩展
```shell
phpize 或者 /usr/local/php7/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config 
sudo make
sudo make install
```
##### 4. 到此时 `phpredis` 编译完成,开始配置 `phpredis`
```shell
sudo vi /etc/php/php.ini
#添加so
extension=redis.so
```
##### 5. 查看扩展是否安装成功
```shell
php -m 
```
### 附录.

#### 1.具体使用方法请参考[redis中文网](http://www.redis.cn/)

#### 2. redis 源码下载地址  [http://download.redis.io/releases/](http://download.redis.io/releases/)
