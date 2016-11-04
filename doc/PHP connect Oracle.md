###PHP 远程连接 Oracle
####1. 下载 Oracle 客户端 Instant Client
***
<font color=#ff00 size=4>注意：</font>windows只支持32位，不支持64位
[下载地址](http://www.oracle.com/technetwork/topics/winsoft-085727.html)

####2. 解压
解压安装包，无需安装

####3. 设置环境变量
ORACLE_HOME 值为instant client所在目录路径:
<br/>
example:
```
解压到D:\instantclient_11_2
添加变量 
变量名： ORACLE_HOME
变量值：D:\instantclient_11_2
```
####4. 开启PHP扩展	
打开 php_oci8扩展 php_pdo_oci扩展
####5. 解决乱码
乱码产生的原因是oracle客户端连接时,跟oracle数据库使用的编码不一致.
<br/>
查询Oracle字符集:
```sql
select * from nls_database_parameters;
```
```php
oci_connect('username','password','connectStr','Oracle字符集');
```
