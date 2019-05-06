错误日志
========

>时间：2016-09-02  
>问题：不小心删除了一些重要文件,导致无法启动Apache

再次重新部署Apache和PHP服务(Apache环境，选的是线程安全的PHP引擎）

1. http.conf配置，似乎就是修改一下Apache24目录，然后修改一些80端口；
2. 启动bin目录下的httpd程序启动apache服务，出现套接字错误，显示端口443错误。修改conf/extra目录下的http-ssl.conf文件中的443端口为442，再去尝试，还是出现错误；又把conf/original/extra/目录下的http-ssl.conf文件中的端口改了，居然还是没有作用。我开始怀疑人生了！直到......我关闭了一切VM服务！成功启动了，但我还是继续压抑着，WTF!
3. 配置apache解析php；
+ 添加LoadModule php5_module到"/php/php5apache2_4.dll";
+ 添加AddType application/x-httpd-php .php .phtml 
+ 添加phpinidir到"/php/" 设置php配置文件目录
+ 将php安装目录下的php.ini-devlopment文件重命名为php.ini;
+ 在php.ini中修改以下几项：  
  ; On windows:  
　;extension_dir = "D:/../php/ext"  
　;extension=php_curl.dll  
　;extension=php_mysql.dll#部署MySQL  

......
尝试“httpd -k restart”重启过程中出现错误：AH00436: No installed service named "Apache2.4".
1)搜索后在overflow中找到答案:"just do a "httpd -k install" and the windows service is added to the registry"
2)以管理员身份运行cmd，输入命令:httpd -k install，再次输入其他http -k命令后无错误提示
3）如果在install期间出现套接字错误，原因可能是没有关闭apache服务。
4）如出现错误：AH00373: Failed to open the 'Apache2.4' service，则可能是没有管理员权限。

......
期间曾出现打开php页面弹出下载框的错误，原因估计是在修改httpd.conf文件时出现：
...
#LoadModule vhost_alias_module modules/mod_vhost_alias.so#其实这儿不用修改
LoadModule php5_module "D:/Web/php/php5apache2_4.dll"
...
phpIniDir "D:/Web/php"
...
AddType application/x-gzip .gz .tgz
AddType application/x-httpd-php .php
...都删除后一步一步撤回修改，虽然成功解析，但是不在弹出下载框，所以寻找原因失败，做个标记吧！

似乎又是忙活了半天，但总算解析成功了！


开始部署MySQL：
-----------------------------------------------------
（1）配置好环境变量（方便一些）后，运行mysqld --install,期间出现错误：
--打开普通cmd.exe出现Install/Remove of the Service Denied!，改成以管理员权限运行即可成功安装mysql服务；

(2)net start mysql出现“MySQL 服务无法启动”，发现没有执行mysqld --initialize初始化，执行后出现错误：
------
2016-09-02T09:41:59.516576Z 0 [ERROR] --initialize specified but the data directory has files in it. Aborting.
2016-09-02T09:41:59.532213Z 0 [ERROR] Aborting
------表示datadir指定的目录下已有数据文件
删除data目录后执行mysqld --initialize，没有显示结果，表示初始化成功。继续执行net start mysql还是无法启动。之后找到：如果mysql 不能启动，需要删除data目录，然后重新运行 mysqld --initialize（来自某吧）,然而问题还是没有解决。最后是mysqld -remove卸载mysql服务后，再依次执行:mysqld --install;mysql --initialize;net start mysql后成功启动MySQL服务。
--为什么我总是一波三折啊！
--期间可能无法删除data目录，不知道因为什么占据文件，建议使用强力删除。

（3）mysql -u root -p进入mysql服务；进入需要密码，在data目录下有个err文件，在里面查找password密码，在密码前有个空格，要去掉才能登陆。

（4）show databases出现错误：ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql> UPDATE mysql.user SET Password=PASSWORD('your_new_password') 
       WHERE User='root';
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql> UPDATE user SET Password=PASSWORD('kj1875_wy') where USER='root';
ERROR 1046 (3D000): No database selected
mysql> SET PASSWORD=PASSWORD('kj1875_wy');
Query OK, 0 rows affected, 1 warning (0.00 sec)
成功修改密码。


查看端口监听情况：
netstat -ano
TCP 127.0.0.1:8088  0.0.0.0:0  LISTENING 12168
停止某项进程：
taskkill /pid 12168 /f















