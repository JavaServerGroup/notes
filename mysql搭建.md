# 搭建MySQL数据库服务器

## yum安装
```shell
## CentOS 7 and Red Hat (RHEL) 7 ##
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

## CentOS 6 and Red Hat (RHEL) 6 ##
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el6-11.noarch.rpm

yum install mysql-community-server
```
## 启动
```shell
service mysqld start
chkconfig mysqld on
```

## 运行安全设置
```shell
grep 'A temporary password is generated for root@localhost' /var/log/mysqld.log |tail -1

/usr/bin/mysql_secure_installation
```

## 链接mysql
```shell
mysql -h localhost -u root -p
```

##### 【<a name="MySQL服务器的配置" id="MySQL服务器的配置"><font color=black>MySQL服务器的配置</font></a>】
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
  



## 遇到问题
先卸载，然后rm -rf /var/lib/mysql 后再装


