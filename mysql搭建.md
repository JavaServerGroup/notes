##搭建MySQL数据库服务器





#####【知识目标】

1. <a href="数据库服务器的基本概念">数据库服务器的基本概念</a>
2. <a href="MySQL的简介">MySQL的简介</a>
3. <a href="#MySQL服务器的安装">MySQL服务器的安装</a>
4. <a href="#MySQL服务器的配置">MySQL服务器的配置</a>




#####【<a name="数据库服务器的基本概念" id="数据库服务器的基本概念" ><font color=black>数据库服务器的基本概念</font></a>】

* 数据库
* 数据库管理系统(DBMS)
* 数据库系统(DBS)
* 数据库服务器
* 数据库分为三种基本形式 :
 *  关系型数据库
 *  层次型数据库
 *  网状型数据库
* 运行在[Linux](http://www.linux.org/resources/)系统上的关系型数据库管理系统主要产品：
    * 企业级服务器:[Oracle](http://www.oracle.com/index.html)、[Sybase](http://www.sybaseproducts.com/)、[DB2](http://www-01.ibm.com/software/data/db2/)
    * 中小型服务器:[MySQL](http://www.mysql.com/)、[PostgreSQL](http://www.postgresql.org/)


#####【<a name="MySQL的简介" id="MySQL的简介"><font color=black>MySQL的简介</font></a>】   

 * 可以运行在不同平台上,支持多用户、多线程和多CPU,没有内存溢出漏洞;
 * 提供多种数据类型,支持ODBC、SSL、支持多种语言利用MySQL的API进行开发; 
 * 是目前市场上现有产品中运行速度最快的数据库系统;
 * 同时访问数据库的用户数量不受限制;
 * 可以保存超过50,000,000条记录;
 * 用户权限设置简单、有效。

#####【<a name="MySQL服务器的安装" id="MySQL服务器的安装"><font color=black>MySQL服务器的安装</font></a>】
   方法一：在线安装
  
  * 步骤1:以root身份登录到CentOS系统；
  * 步骤2:查看系统中是否已安装mysql软件,若无任何显示表明未安装，如果有安装，则移除（rpm –e 名称），如果未能移除，则用rpm --nodeps 名称或是其他强制移除命令；
    ```html
    [root@centos ~]# rpm -qa | grep mysql  ← 查看系统中是否已安装mysql软件

  ```
  * 步骤3:若是有网络可以直接通过命令安装，CentOS系统的命令是yum install mysql-server，而Ubuntu系统的命令是apt-get install mysql-server；
   ```html
    [root@centos ~]# yum install mysql-server <-- install mysql-server

   ```
  * 步骤4:启动mysql，并让MySQL在系统重新启动后随系统自动启动；
    ```html
    [root@centos ~]# chkconfig mysqld on  ← 设置MySQL服务随系统启动自启动
    [root@centos ~]# chkconfig --list mysqld　 ← 确认MySQL自启动
    
     mysqld  0:off 1:off 2:on 3:on 4:on 5:on 6:off　← 如果2--5为on的状态就OK

    [root@centos ~]# /etc/rc.d/init.d/mysqld start　 ← 启动MySQL服务

     Initializing MySQL database:　　　　　　　[ OK ]
     Starting MySQL: 　　　　　　　　　　　　 　[ OK ]
     
   ```


方法二：rpm包安装
   
  * 步骤1:以root身份登录到CentOS系统；
  * 步骤2:查看系统中是否已安装mysql软件,若无任何显示表明未安装，若有安装，则移除（rpm –e 名称），若未能移除，则用rpm --nodeps 名称或是其他强制移除命令；
  
  ```html
    [root@centos ~]# rpm -qa | grep mysql  ← 查看系统中是否已安装mysql软件

  ```
  * 步骤3:下载Linux对应的RPM包，此处以MySQL-server-5.5.46-1.el7.x86_64.rpm为例，路径默认；
  
 ```html
    [root@centos ~]# rpm -ivh MySQL-server-5.5.46-1.el7.x86_64.rpm ← 安装mysql

  ```
   


#####【<a name="MySQL服务器的配置" id="MySQL服务器的配置"><font color=black>MySQL服务器的配置</font></a>】
 * 步骤1:设置MySQL的root用户密码；
     ```html
    [root@centos ~]# mysql -u root　 ← 用root用户登录MySQL服务器

    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 19
    Server version: 5.1.73 Source distribution

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> select user,host,password from mysql.user;　 ← 查看用户信息
   
    +--------+-------------------+-----------------------------------------+
    |  user  | host              | password                                |
    +--------+-------------------+-----------------------------------------+
    |  root  | localhost         |                                         |   ← root密码为空
    |  root  | ip-172-17-20-18   |                                         |
    |  root  | 127.0.0.1         |                                         |
    |        | localhost         |                                         |
    |        | ip-172-17-20-18   |                                         |
    +--------+-----------------+-------------------------------------------+
    5 rows in set (0.00 sec)
   mysql> set password for root@localhost=password('root密码');　← 设置root密码 
   
   Query OK, 0 rows affected (0.01 sec)
   
   mysql> select user,host,password from mysql.user;　 ← 查看用户信息
   
   +--------+--------------------+-----------------------------------------+
   |  user  | host　　　　　　  　 | password　     　　                     |
   +--------+--------------------+-----------------------------------------+
   |  root  | localhost　　　　   | 19b68057189b027f                        |　 ← root密码被设置
   |  root  | ip-172-17-20-18　　|                                         |　 
   |  root　| 127.0.0.1          | 　　　　　　　                          　|
   | 　　   | localhost　　　　　  |　　　　　　　   　                        |
   |        | ip-172-17-20-18    |                                         |
   +-------+---------------------+-----------------------------------------+
   5 rows in set (0.01 sec)
   
   mysql> exit　 ← 退出MySQL服务器
   
    Bye

   ```
 * 步骤2:测试一下root密码是否生效
  ```html
    [root@centos ~]# mysql -u root -p　 ← 通过密码用root登录
    Enter password:　 ← 在这里输入密码

   Welcome to the MySQL monitor.  Commands end with ; or \g.  
    Your MySQL connection id is 19   ← 确认用密码能够成功登录
    Server version: 5.1.73 Source distribution

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> exit
    
    Bye
  ```
 * 步骤3:创建并授权新用户
  ```html
    [root@centos ~]# mysql -u root -p　 ← 通过密码用root登录
    Enter password:　 ← 在这里输入密码

    Welcome to the MySQL monitor.  Commands end with ; or \g.  
    Your MySQL connection id is 19   
    Server version: 5.1.73 Source distribution

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> grant select on *.* to dengli@localhost identified by '123456';　 ← 建立对任意数据库、表有查看操作权限的名为dengli的用户，密码是123456
    
    Query OK, 0 rows affected (0.03 sec)
    
    mysql> select user,host,password from mysql.user;　 ← 确认dengli用户的存在与否
    
    +--------+---------------------+--------------------------------------+ 
    |  user  | host　　　　　　　    | password　     　                   　|
    +--------+---------------------+--------------------------------------+ 
    |  root  | localhost　　　　　  | 19b68057189b027f                     |　
    |  root  | ip-172-17-20-18　 　|                                      |　  
    |  root　| 127.0.0.1           | 　　　　　　　                       　|
    | 　　   | localhost　　　　  　 |　　　　　　　   　                     |
    |        | ip-172-17-20-18     |                                      | 
    | dengli | %                   | 49ba59abbe56e057                     |  ← root密码被设置 
    +------+-----------------------+--------------------------------------+ 
    
    6 row in set (0.01 sec)
    
    mysql> exit　 ← 退出MySQL服务器
    
    Bye
    
    [root@centos ~]# mysql -u centospub -p　 ← 用新建立的dengli用户登录MySQL服务器
    Enter password:　 ← 在这里输入密码
     Welcome to the MySQL monitor.  Commands end with ; or \g.  
    Your MySQL connection id is 19   
    Server version: 5.1.73 Source distribution

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> show grants for dengli@'%';
    
    +------------------------------------------------------------------------------------+
    | Grants for dengli@%                                                                |
    +------------------------------------------------------------------------------------+
    | GRANT SELECT ON *.* TO 'dengli'@'%'IDENTIFIED BY PASSWORD '49ba59abbe56e057'       |
    +------------------------------------------------------------------------------------+
    1 row in set (0.00 sec)
    
  ```
 * 步骤4:设置连接端口（3306）
  ```html
  [root@centos ~]# vim /etc/sysconfig/iptables
  ```
 在iptables的文件中添加 -I INPUT -p tcp --dport 3306 -j ACCEPT，然后保存退出。

 到此已经完成了搭建MySQL数据库服务器！   
  






