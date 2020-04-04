04.04 12:38
【termux】记一次termux安装MariaDB(MySQL)数据库中的问题及解决方法

总所周知Termux是一个Android下一个高级的终端模拟器,开源且不需要root,支持apt管理软件包，十分方便安装软件包,完美支持Python,PHP,Ruby,Go,Nodejs,MySQL等。随着智能设备的普及和性能的不断提升，如今的手机、平板等的硬件标准已达到了初级桌面计算机的硬件标准,用心去打造完全可以把手机变成一个强大的工具.
MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。开发这个分支的原因之一是：甲骨文公司收购了MySQL后，有将MySQL闭源的潜在风险，因此社区采用分支的方式来避开这个风险。

开始
1 安装MariaDB

pkg install mariadb
1
2 安装基本数据

mysql_install_db
1
启动完成后,这个会话就一直存活,类似与debug调试一样,只有新建会话才可以操作,记得新建会话，左滑，下面有一个NEW SESSION



关于隐藏会话可以使用nohup命令和tmux命令,这里我建议使用tmux命令

3 修改密码

我遇到的坑要来了，可能很多和我一样的新手也遇过这个问题

由于mariadb安装的时候没有设置密码,当前的mariadb密码为空.
输入下面代码进入数据库

mysql



ps:这张图来自国光博客，我自己已经安装好了怕嫌麻烦
接下来输入下面代码

mysql_secure_installation

####测试到这里备注

按理是没有密码，但是回车却错误，这个时候我们退出去，重新进入数据库输入代码

set password for root@localhost =password("newpassword");
1
这个时候我们就设置好了密码。为newpadsword
现在就可以安装了！

####测试到这里备注

