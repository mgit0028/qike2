04.04 13:03
用termux 安装mysql分支 mariadb数据库
04.03 21:11
使用Termux安装Mysql数据库夸克搜索
使用Termux安装Mysql数据库
 

termux
此处安装termux不建议安装酷安版本.请从F-droid里面下载,因为以后你可能会用到termux的插件,如果你从酷安安装的话.以后安装插件的时候会报 签名不一致 而无法安装成功.
配置环境:

安装好termux后更新终端
修改软件更新源,就不写了.自行百度
$ apt update
$ apt upgrade
安装 Mariadb
$ pkg install mariadb
软件环境安装好以后,开始做一些必要的配置.也是踩了很多坑踩成功把mysql跑起来
需要创建一个文件夹，新版的termux也许不用创建文件夹已有或没有，新版的termux0.92版

##备注######新版的也许不用创建文件夹

$ cd /data/data/com.termux/files/usr/etc/
//创建文件夹并没有报错，$ cd  和/data之间有一个空格，必须加一个空格

$ mkdir my.cnf.d  #这里省略

//运行到这里mkdir: cannot create directory ‘my.cnf.d’: File exists
mkdir:无法创建目录”:文件存在

文件夹创建好以后准备安装数据库

$ mysql_install_db  #这里省略

//运行到这里提示
mysql.user table already exists!
Run mysql_upgrade, not mysql_install_db

安装好以后.开始初始启动数据库服务

##备注######新版的省略上面三步

$ mysqld --user=root
第一次启动后.终端可能会卡死...不知道是手机的原因还是什么原因.我重启了一下终端.
启动mysql服务成功以后.需要初始化一下mysql

//测试到这里没有卡死

$ mysql_secure_installation
初始化数据库是需要你手动做一些选择
第一处暂停的地方


NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none)://运行到这里后这里让你输入数据库的密码.因为我们是第一次安装.所以没有密码.直接按回车键


NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL

      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MySQL to secure it, we'll need the current

password for the root user.  If you've just installed MySQL, and

you haven't set the root password yet, the password will be blank,

so you should just press enter here.

Enter current password for root (enter for none):
这里让你输入数据库的密码.因为我们是第一次安装.所以没有密码.直接按回车键


mysql_secure_installation

####测试到这里备注

按理是没有密码，但是回车却错误，这个时候我们退出去，重新进入数据库输入代码

set password for root@localhost =password("newpassword");
1
这个时候我们就设置好了密码。为newpadsword
现在就可以安装了！

####测试到这里备注

第二步://这里没有测试省略

OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MySQL

root user without the proper authorisation.

 

Set root password? [Y/n] y
这里询问是否设置数据库用户 root 的密码, 当然要输入y
接下来输入两次密码

New password: 

Re-enter new password: 

Password updated successfully!

Reloading privilege tables..

 ... Success!

By default, a MySQL installation has an anonymous user, allowing anyone

to log into MySQL without having to have a user account created for

them.  This is intended only for testing, and to make the installation

go a bit smoother.  You should remove them before moving into a

production environment.

Remove anonymous users? [Y/n] y
这里询问是否删除匿名用户? 当然输入y 删除
继续下一步

 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This

ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] 
这里询问是否禁用root用户从远程登录?
如果你需要从远程登录到数据库就输入 n
如果你只从本机进行数据库管理就输入 y

By default, MySQL comes with a database named 'test' that anyone can

access.  This is also intended only for testing, and should be removed

before moving into a production environment.

Remove test database and access to it? [Y/n] y
这里询问是否删除 test 数据库? 没什么用 输入y 删除吧

 - Dropping test database...

 ... Success!

 - Removing privileges on test database...

 ... Success!

Reloading the privilege tables will ensure that all changes made so far

will take effect immediately.

Reload privilege tables now? [Y/n] y
这里询问是否重建权限表, 输入 y

 ... Success!

All done!  If you've completed all of the above steps, your MySQL

installation should now be secure.

Thanks for using MySQL!

Cleaning up...
到这里,数据库就初始化完成

本机登录mysql

$ mysql -u root -p
Enter password:
输入你刚才设置的密码....

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 24
Server version: 10.3.11-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
输入 show databases;
必须以 ; 号结尾

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.004 sec)

MariaDB [(none)]>
至此 termux安装数据库完成.

但此时如果使用Navicat等数据库管理工具链接的话.会爆 拒绝连接.原因是 root的登录方式为 loaclhost.这里需要修改一下.

使用命令:

use mysql; //更改所操作的数据库

update user set host='%' where host='localhost'; //更改host的值为% 条件是host等于localhost的这一条数据

flush privileges; //刷新用户权限表

MariaDB [(none)]> use mysql;
Database changed
MariaDB [mysql]> select host,user,password from user;
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | root | *796D73744CD9F75E1C4FCF7C7F47E1668A99C125 |
| 127.0.0.1 | root | *796D73744CD9F75E1C4FCF7C7F47E1668A99C125 |
| ::1       | root | *796D73744CD9F75E1C4FCF7C7F47E1668A99C125 |
+-----------+------+-------------------------------------------+
3 rows in set (0.001 sec)

MariaDB [mysql]> update user set host='%' where host='localhost';
Query OK, 0 rows affected (0.002 sec)
Rows matched: 0  Changed: 0  Warnings: 0

MariaDB [mysql]> flush privileges;
Query OK, 0 rows affected (0.005 sec)

//最后查看一下修改后的 user 表
MariaDB [mysql]> select host,user,password from user;
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| %         | root | *796D73744CD9F75E1C4FCF7C7F47E1668A99C125 |
| 127.0.0.1 | root | *796D73744CD9F75E1C4FCF7C7F47E1668A99C125 |
| ::1       | root | *796D73744CD9F75E1C4FCF7C7F47E1668A99C125 |
+-----------+------+-------------------------------------------+
3 rows in set (0.002 sec)

完成以上操作,再在Navicat等管理工具上连接数据库时.即可正常连接了



https://www.autojs.org/topic/3404/%E4%BD%BF%E7%94%A8termux%E5%AE%89%E8%A3%85mysql%E6%95%B0%E6%8D%AE%E5%BA%93




更多termux安装方法


https://www.csdn.net/gather_22/MtTaEg2sNDc1Ni1ibG9n.html




