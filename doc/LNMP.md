## Install LNMP for Ubuntu
***
##1. 编译环境说明
###1.1  **写在前面的的话**

1) 由于目前最新的PHP版本是php-7.1.0beta1，所以使用的是该版本。Swoole目前最新1.9.0-stable，所以使用的是该版本。

2) 系统环境
```table
系统(-) | 版本(-)
Ubuntu | 14.04.1 LTS
```
3) 编译环境
*想要编译安装PHP首先需要安装对应的编译工具。 Ubuntu上使用如下命令安装编译工具和依赖包：*
```shell
sudo apt-get -y install \
build-essential \
gcc \
g++ \
autoconf \
libiconv-hook-dev \
libmcrypt-dev \
libxml2-dev \
libmysqlclient-dev \
libcurl4-openssl-dev \
libjpeg8-dev \
libpng12-dev \
libfreetype6-dev \
如果是ubuntu16.04 ，还需
sudo apt-get install libssl-dev 
sudo apt-get install openssl
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-11-18-49.jpg)

##2. 编译安装PHP
###2.1 **编译安装PHP7**
1)	下载php-7.1.0beta1源码到/opt目录(一般都是编译目录都是/opt,其他目录也可,赋予777权限)。
```shell
sudo chmod 777 ./
cd /opt
wget http://downloads.php.net/~ab/old/php-7.1.0beta1.tar.gz
```

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-11-14-56.jpg)
2)	下载的比较慢，等下载完，解压php-7.1.0beta1.tar.gz,有可能有些警告忽略就行了。
```shell
tar -zvxf php-7.1.0beta1.tar.gz
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-11-39-45.jpg)

3)	解压完毕，看到php-7.1.0beta1的文件夹，进入该文件夹，指定编译参数(建议写成一个脚本，防止出错后重复写)。
```shell
cd php-7.1.0beta1/
vi configure_php.sh
########################以下是configure_php.sh script 内容############################
#!/bin/bash
./configure --prefix=/usr/local/php \
--with-config-file-path=/etc/php \
--enable-fpm \
--enable-pcntl \
--enable-mysqlnd \
--enable-opcache \
--enable-sockets \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-shmop \
--enable-zip \
--enable-soap \
--enable-xml \
--enable-mbstring \
--enable-gd-native-ttf \
--disable-rpath \
--with-iconv-dir \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib=/usr \
--with-libxml-dir=/usr \
--disable-debug \
--disable-fileinfo \
--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-pcre-regex \
--with-iconv \
--with-zlib \
--with-mcrypt \
--with-gd \
--with-openssl \
--with-mhash \
--with-xmlrpc \
--with-curl \
--with-imap-ssl
```
注意，以上PHP编译选项根据实际情况可调整。（**查看编译参数 ./configure --help**）

4)  运行该脚本, 注意查看是否有*error*, 如有自行*Google*, 这里就不做详细的介绍了.
```shell
chmod +x configure_php.sh
./configure_php.sh
```  
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-11-48-17.jpg)

5)  编译文件, 编译的速度跟机器的配置有关，也许有Warning请忽视.
```shell
# -j4 可选参数 如果是多核可用，加快编译速度，具体请Google
make [-j4]
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-11-53-33.jpg)
6) 安装PHP
```shell
sudo make install
```

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-13-01-15.jpg)

7) 安装完成，创建php配置文件目录，并且复制php.ini到该目录.
```shell
sudo mkdir /etc/php
sudo cp php.ini-development /etc/php/php.ini
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-13-03-27.jpg)

8) **添加环境变量**，打开系统环境变量目录，在末尾添加,保存后，在终端输入source /etc/profile.
```shell
sudo vi /etc/profile
#要添加的内容
export PATH=/usr/local/php/bin:$PATH
export PATH=/usr/local/php/sbin:$PATH
source /etc/profile
######################################
sudo vi /etc/environment
#要添加内容在末尾添加
/usr/local/php/bin:/usr/local/php/sbin:
source /etc/environment
```
完成以上步骤，PHP安装完成。
可用php -v 查看PHP版本
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-02-19.jpg)
###2.2 编译安装Swoole扩展(需要可用)

1)	下载Swoole源码到/opt
```shell
cd /opt
 wget -c https://codeload.github.com/swoole/swoole-src/tar.gz/v1.9.0-stable
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-35-30.jpg)

2)下载成功，解压v1.9.0-stable。
```shell
tar -zvxf v1.9.0-stable
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-37-00.jpg)

3)	进入Swoole目录，phpize(加载php扩展，官方说明，参见：[http://php.net/manual/en/install.pecl.phpize.php](http://php.net/manual/en/install.pecl.phpize.php))
```shell
cd swoole-src-1.9.0-stable/
phpize
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-40-52.jpg)

4)	指定编译参数。(*swoole*的*./configure*有很多额外参数，可以通过*./configure --help*命令查看,这里均选择默认项)
```shell
./configure --with-php-config=/usr/local/php/bin/php-config
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-43-17.jpg)

5)	指定编译参数的时候看看是否有error(画红线的部分)，如果有，请Google，这里就不做详细说明了。编译文件。
```shell
make
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-44-14.jpg)

6)	安装Swoole
```shell
make install
```
安装完成后，进入/etc/php目录下，打开php.ini文件，在其中加上如下一句：
```shell
extension=swoole.so
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-46-26.jpg)
随后在终端中输入命令php -m查看扩展安装情况。如果在列出的扩展中看到了swoole，则说明安装成功。
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-14-47-59.jpg)
##3. **编译安装Nginx与配置**
###3.1 **FastCGI和Ngnix+FastCGI的运行原理**
>Nginx本身不能处理PHP，它只是个web服务器，当接收到请求后，如果是php请求，则发给php解释器处理，并把结果返回给客户端。
Nginx一般是把请求发fastcgi管理进程处理，fascgi管理进程选择cgi子进程处理结果并返回被Nginx。
####1、apache+PHP和ngnix+php的区别
apache一般是把php当做自己的一个模块来启动；而ngnix则是把http请求变量转发给php进程，即php独立进程，与ngnix通信，这种方式叫做FastCGI运行方式。
所以，apache所编译的php不能用于ngnix。
2、什么是FastCGI呢？
FastCGI是一个可伸缩地、高速地在HTTP server和动态脚本语言间通信的接口。多数流行的HTTP server都支持FastCGI，包括Apache、Nginx和lighttpd等。同时，FastCGI也被许多脚本语言支持，其中就有PHP。
FastCGI是从CGI发展改进而来的。传统CGI接口方式的主要缺点是性能很差，因为每次HTTP服务器遇到动态程序时都需要重新启动脚本解析器来执行解析，然后将结果返回给HTTP服务器。这在处理高并发访问时几乎是不可用的。另外传统的CGI接口方式安全性也很差，现在已经很少使用了。
FastCGI接口方式采用C/S结构，可以将HTTP服务器和脚本解析服务器分开，同时在脚本解析服务器上启动一个或者多个脚本解析守护进程。当HTTP服务器每次遇到动态程序时，可以将其直接交付给FastCGI进程来执行，然后将得到的结果返回给浏览器。这种方式可以让HTTP服务器专一地处理静态请求或者将动态脚本服务器的结果返回给客户端，这在很大程度上提高了整个应用系统的性能。
####2、Nginx+FastCGI运行原理　
Nginx不支持对外部程序的直接调用或者解析，所有的外部程序（包括PHP）必须通过FastCGI接口来调用。FastCGI接口在Linux下是socket（这个socket可以是文件socket，也可以是ip socket）。
wrapper：为了调用CGI程序，还需要一个FastCGI的wrapper（wrapper可以理解为用于启动另一个程序的程序），这个wrapper绑定在某个固定socket上，如端口或者文件socket。当Nginx将CGI请求发送给这个socket的时候，通过FastCGI接口，wrapper接收到请求，然后Fork(派生）出一个新的线程，这个线程调用解释器或者外部程序处理脚本并读取返回数据；接着，wrapper再将返回的数据通过FastCGI接口，沿着固定的socket传递给Nginx；最后，Nginx将返回的数据（html页面或者图片）发送给客户端。这就是Nginx+FastCGI的整个运作过程，
>所以，我们首先需要一个wrapper，这个wrapper需要完成的工作：
1) 通过调用fastcgi（库）的函数通过socket和ningx通信（读写socket是fastcgi内部实现的功能，对wrapper是非透明的）
2) 调度thread，进行fork和kill
3) 和application（php）进行通信

###3.2 **Nginx安装**
1) 下载安装nginx
```shell
sudo apt-get install nginx
```
2) nginx配置文件修改:
```shell
sudo vi /etc/nginx/sites-available/default
#把index index.html index.htm修改为
index index.html index.htm index.php
#配置Fastcgi解析php脚本
location ~ \.php$ {
                #fastcgi_split_path_info^(.+\.php)(/.+)$;
                # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini

                # With php5-cgi alone:
                fastcgi_pass 127.0.0.1:9000;
                # With php5-fpm:
                #fastcgi_passunix:/var/run/php5-fpm.sock;
                fastcgi_indexindex.php;
                includefastcgi_params;
        }
#如果配置支持ThinkPHP需要在
location / {
		#原有配置
	if (!-e $request_filename) {
            #如果项目在根目录使用下面这个rewrite规则 (根目录指的是：设置的web根目录 如果网站目录在web根目录下就是子目录)
            #rewrite  ^(.*)$  /index.php?s=$1  last;
            #如果项目在子目录使用下面这个rewrite规则
            rewrite ^/子目录/(.*)$ /子目录/index.php?s=/$1 last;
            break;
    }
}
```
3) 启用php-fpm 
上面我们在编译php7的时候，已经将fpm模块编译了，那么接下来，我们要启用php-fpm。但是默认情况下它的配置文件和服务都没有启用，所以要我们自己来搞定。
```shell
cd /usr/local/php/etc
sudo mv php-fpm.conf.default  php-fpm.conf
sudo mv php-fpm.d/www.conf.default  php-fpm.d/www.conf
#配置nginx监听的用户
sudo vi php-fpm.d/www.conf
#把该文件里的
user=nobody
group = nobody
#改为
user = www-data
group = www-data
###启动php-fpm
sudo /usr/local/php/sbin/php-fpm
#把php-fpm设置成开机自启
sudo vi /etc/rc.local
#添加
/usr/local/php/sbin/php-fpm
#######################################
#把开启php-fpm命令修改为  /etc/init.d/php-fpm
sudo cp /usr/local/php/sbin/php-fpm /etc/init.d/
```
##4. 安装mysql
```shell
sudo apt-get install mysql-server mysql-client
```
期间会提示你是否安装【Y/n】，输入y，安装好之后会提示你输入`mysql root`
用户的密码，输入了按回车.

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-13-00.jpg)

此时会提示你再次输入，你就再输入，必须与刚才的密码一致.

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-13-19.jpg)   

输入完成按回车，等待安装完成，mysql就安装好了.

##5. 安装phpMyadmin

1) 下载安装包phpMyAdmin-4.6.3-all-languages.tar.gz

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-14-51.jpg)

2) 解压到任意目录(建议解压到/var/www/)

```shell
cd  /var/www
sudo tar -zvxf phpMyAdmin-4.6.3-all-languages.tar.gz
sudo mv phpMyAdmin-4.6.3-all-languages  phpmyadmin
```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-16-46.jpg)

3) 解压完成后可以看到一个文件夹phpMyAdmin-4.6.3-all-languages，进入该文件夹，拷贝配置文件,打开配置页面，修改$cfg['blowfish_secret'] 的值，

```shell
#如:
($cfg['blowfish_secret'] = 'www.airocov.com').
#修改
$cfg['Servers'][$i]['host'] = 'localhost' 
#改为
$cfg['Servers'][$i]['host'] = '127.0.0.1'
#(防止出现2002错误) .
#删除Storage database and tables这个下面所有选项的注释，到此配置结束。
```

```shell
cd  phpmyadmin/
sudo   cp   config.sample.inc.php  config.inc.php
sudo  vi  config.inc.php
```

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-25-19.jpg)

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-25-27.jpg)

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-25-40.jpg)

4) 创建Nginx下关于phpmyadmin的配置文件，并修改配置。

```shell
sudo vi  /etc/nginx/conf.d/phpmyadmin.conf
#复制以下内容
server {
		#访问phpmyadmin的端口，可改（要同时改变两个）
        listen 8081 default_server;
        listen [::]:8081 default_server ipv6only=on;
		#该地址为目录的解压地址，根据实际情况
        root /var/www/phpmyadmin;
		#Nginx解析的内容
        index index.html index.htm index.php;

        # Make site accessible from http://localhost/
        server_name localhost;

        location / {
				#该地址为目录的解压地址，根据实际情况
                root /var/www/phpmyadmin;
				#解析的内容
                index  index.php;
            }

        location ~ \.php$ {
				#该地址为目录的解压地址，根据实际情况
                root /var/www/phpmyadmin;
                #fastcgi_passunix:/var/run/php-fpm/php-fpm.sock;
				#看自己服务器上的fpm监听方式
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index  index.php;
                #fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include  fastcgi_params;
            }

        location ~ /\.ht {
                deny all;
            }
}
保存该文件,重启Ngingx.
sudo service nginx restart
```

5) 导入phpmyadmin的配置表
进入mysql,创建名为phpmyadmin的数据库，导入create_tables.sql(该文件在phpmyadmin安装包的sql文件夹下)。
mysql  -uroot -p密码
按回车进入mysql

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-27-22.jpg)

```shell
#创建数据库phpmyadmin
create database phpmyadmin
#选中数据库，并导入sql文件
use  phpmyadmin
source  /var/www/phpmyadmin/sql/create_tables.sql

```
![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-28-13.jpg)

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-28-35.jpg)

6) 打开浏览器输入服务器的域名:端口号，查看是否看见登录。（用mysql的账号登录），安装成功

![](https://github.com/wp-Breeder/Personal-essay/blob/master/doc/_images/LNMP/2016-12-01-18-29-12.jpg)
