

### 一、编译安装redis
----
#### 1.下载 redis
> 1.进入编译目录，这里我选择的`/opt`目录。

		sudo chmod 777 /opt
		cd /opt
      	sudo wget http://download.redis.io/releases/redis-3.2.2.tar.gz
>2.下载完成后，解压`redis`。

		sudo tar -zvxf redis-3.2.2.tar.gz

>3.因为redis本身就是makefile,所以编译起来非常简单，直接进入源码目录，make即可。

		cd  cd redis-3.2.2/
		sudo make
		sudo make PREFIX=/usr/local/redis-3.2.2 install
		sudo cp redis.conf /usr/local/redis-3.2.2/

>4.到此时，`redis` 已经安装完成了，接下来配置 `redis`。

#### 2.配置redis
>1.启动服务,修改配置文件，将其中的`daemonize no`行改为d`aemonize yes`，让其在后台运行。

		cd /usr/local/redis-3.2.2/
		sudo vi redis.conf
		把  daemonize no 改为 aemonize yes，保存并退出

>2.启动redis服务，为了以后更加方便，设置开机启动，并且添加系统环境变量。

		sudo vi /etc/profile

>并且在最后添加 `export PATH=/usr/local/redis-3.2.2/bin:$PATH` 保存并退出,复制配置文件到`/etc/`

		source /etc/profile
		sudo ln -s ../local/redis-3.2.2/bin/* .

>执行 `source /etc/profile` 立即生效，以后redis的启动服务命令是：

		sudo redis-server /usr/local/redis-3.2.2/redis.conf

>3.查看`redis`服务是否正常启动。

		 ps -aux |grep redis

>4.关闭`redis`的命令为

		 sudo redis-cli shutdown

>5.进入`redis`客户端的命令为

		 redis-cli
### 二、编译安装 phpredis-php7 扩展

>1.下载 `phpredis` 扩展源码

		cd /opt
		wget -c https://github.com/phpredis/phpredis/archive/php7.zip

>2.解压 `php7.zip`

		sudo apt-get instarll zip

>安装完成后解压 `php7.zip`

		unzip php7.zip

>解压完成后，进入 `phpredis-php7` 目录

		cd phpredis-php7/

>3.开始编译phpredis扩展

		phpize 或者 /usr/local/php7/bin/phpize
		./configure --with-php-config=/usr/local/php/bin/php-config 
		sudo make
		sudo make install

>4.到此时 `phpredis` 编译完成,开始配置 `phpredis`

		sudo vi /etc/php/php.ini
        #添加so
        extension=redis.so

>5.查看扩展是否安装成功

		php -m 





### 附录.

#### 1.具体使用方法请参考[redis中文网](http://www.redis.cn/)

#### 2. redis 源码下载地址  [http://download.redis.io/releases/](http://download.redis.io/releases/)
