##�MySQL���ݿ������





#####��֪ʶĿ�꡿

1. <a href="���ݿ�������Ļ�������">���ݿ�������Ļ�������</a>
2. <a href="MySQL�ļ��">MySQL�ļ��</a>
3. <a href="#MySQL�������İ�װ">MySQL�������İ�װ</a>
4. <a href="#MySQL������������">MySQL������������</a>




#####��<a name="���ݿ�������Ļ�������" id="���ݿ�������Ļ�������" ><font color=black>���ݿ�������Ļ�������</font></a>��

* ���ݿ�
* ���ݿ����ϵͳ(DBMS)
* ���ݿ�ϵͳ(DBS)
* ���ݿ������
* ���ݿ��Ϊ���ֻ�����ʽ :
 *  ��ϵ�����ݿ�
 *  ��������ݿ�
 *  ��״�����ݿ�
* ������[Linux](http://www.linux.org/resources/)ϵͳ�ϵĹ�ϵ�����ݿ����ϵͳ��Ҫ��Ʒ��
    * ��ҵ��������:[Oracle](http://www.oracle.com/index.html)��[Sybase](http://www.sybaseproducts.com/)��[DB2](http://www-01.ibm.com/software/data/db2/)
    * ��С�ͷ�����:[MySQL](http://www.mysql.com/)��[PostgreSQL](http://www.postgresql.org/)


#####��<a name="MySQL�ļ��" id="MySQL�ļ��"><font color=black>MySQL�ļ��</font></a>��   

 * ���������ڲ�ͬƽ̨��,֧�ֶ��û������̺߳Ͷ�CPU,û���ڴ����©��;
 * �ṩ������������,֧��ODBC��SSL��֧�ֶ�����������MySQL��API���п���; 
 * ��Ŀǰ�г������в�Ʒ�������ٶ��������ݿ�ϵͳ;
 * ͬʱ�������ݿ���û�������������;
 * ���Ա��泬��50,000,000����¼;
 * �û�Ȩ�����ü򵥡���Ч��

#####��<a name="MySQL�������İ�װ" id="MySQL�������İ�װ"><font color=black>MySQL�������İ�װ</font></a>��
   ����һ�����߰�װ
  
  * ����1:��root��ݵ�¼��CentOSϵͳ��
  * ����2:�鿴ϵͳ���Ƿ��Ѱ�װmysql���,�����κ���ʾ����δ��װ������а�װ�����Ƴ���rpm �Ce ���ƣ������δ���Ƴ�������rpm --nodeps ���ƻ�������ǿ���Ƴ����
    ```html
    [root@centos ~]# rpm -qa | grep mysql  �� �鿴ϵͳ���Ƿ��Ѱ�װmysql���

  ```
  * ����3:�������������ֱ��ͨ�����װ��CentOSϵͳ��������yum install mysql-server����Ubuntuϵͳ��������apt-get install mysql-server��
   ```html
    [root@centos ~]# yum install mysql-server <-- install mysql-server

   ```
  * ����4:����mysql������MySQL��ϵͳ������������ϵͳ�Զ�������
    ```html
    [root@centos ~]# chkconfig mysqld on  �� ����MySQL������ϵͳ����������
    [root@centos ~]# chkconfig --list mysqld�� �� ȷ��MySQL������
    
     mysqld  0:off 1:off 2:on 3:on 4:on 5:on 6:off���� ���2--5Ϊon��״̬��OK

    [root@centos ~]# /etc/rc.d/init.d/mysqld start�� �� ����MySQL����

     Initializing MySQL database:��������������[ OK ]
     Starting MySQL: ������������������������ ��[ OK ]
     
   ```


��������rpm����װ
   
  * ����1:��root��ݵ�¼��CentOSϵͳ��
  * ����2:�鿴ϵͳ���Ƿ��Ѱ�װmysql���,�����κ���ʾ����δ��װ�����а�װ�����Ƴ���rpm �Ce ���ƣ�����δ���Ƴ�������rpm --nodeps ���ƻ�������ǿ���Ƴ����
  
  ```html
    [root@centos ~]# rpm -qa | grep mysql  �� �鿴ϵͳ���Ƿ��Ѱ�װmysql���

  ```
  * ����3:����Linux��Ӧ��RPM�����˴���MySQL-server-5.5.46-1.el7.x86_64.rpmΪ����·��Ĭ�ϣ�
  
 ```html
    [root@centos ~]# rpm -ivh MySQL-server-5.5.46-1.el7.x86_64.rpm �� ��װmysql

  ```
   


#####��<a name="MySQL������������" id="MySQL������������"><font color=black>MySQL������������</font></a>��
 * ����1:����MySQL��root�û����룻
     ```html
    [root@centos ~]# mysql -u root�� �� ��root�û���¼MySQL������

    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 19
    Server version: 5.1.73 Source distribution

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> select user,host,password from mysql.user;�� �� �鿴�û���Ϣ
   
    +--------+-------------------+-----------------------------------------+
    |  user  | host              | password                                |
    +--------+-------------------+-----------------------------------------+
    |  root  | localhost         |                                         |   �� root����Ϊ��
    |  root  | ip-172-17-20-18   |                                         |
    |  root  | 127.0.0.1         |                                         |
    |        | localhost         |                                         |
    |        | ip-172-17-20-18   |                                         |
    +--------+-----------------+-------------------------------------------+
    5 rows in set (0.00 sec)
   mysql> set password for root@localhost=password('root����');���� ����root���� 
   
   Query OK, 0 rows affected (0.01 sec)
   
   mysql> select user,host,password from mysql.user;�� �� �鿴�û���Ϣ
   
   +--------+--------------------+-----------------------------------------+
   |  user  | host������������  �� | password��     ����                     |
   +--------+--------------------+-----------------------------------------+
   |  root  | localhost��������   | 19b68057189b027f                        |�� �� root���뱻����
   |  root  | ip-172-17-20-18����|                                         |�� 
   |  root��| 127.0.0.1          | ��������������                          ��|
   | ����   | localhost����������  |��������������   ��                        |
   |        | ip-172-17-20-18    |                                         |
   +-------+---------------------+-----------------------------------------+
   5 rows in set (0.01 sec)
   
   mysql> exit�� �� �˳�MySQL������
   
    Bye

   ```
 * ����2:����һ��root�����Ƿ���Ч
  ```html
    [root@centos ~]# mysql -u root -p�� �� ͨ��������root��¼
    Enter password:�� �� ��������������

   Welcome to the MySQL monitor.  Commands end with ; or \g.  
    Your MySQL connection id is 19   �� ȷ���������ܹ��ɹ���¼
    Server version: 5.1.73 Source distribution

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> exit
    
    Bye
  ```
 * ����3:��������Ȩ���û�
  ```html
    [root@centos ~]# mysql -u root -p�� �� ͨ��������root��¼
    Enter password:�� �� ��������������

    Welcome to the MySQL monitor.  Commands end with ; or \g.  
    Your MySQL connection id is 19   
    Server version: 5.1.73 Source distribution

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> grant select on *.* to dengli@localhost identified by '123456';�� �� �������������ݿ⡢���в鿴����Ȩ�޵���Ϊdengli���û���������123456
    
    Query OK, 0 rows affected (0.03 sec)
    
    mysql> select user,host,password from mysql.user;�� �� ȷ��dengli�û��Ĵ������
    
    +--------+---------------------+--------------------------------------+ 
    |  user  | host��������������    | password��     ��                   ��|
    +--------+---------------------+--------------------------------------+ 
    |  root  | localhost����������  | 19b68057189b027f                     |��
    |  root  | ip-172-17-20-18�� ��|                                      |��  
    |  root��| 127.0.0.1           | ��������������                       ��|
    | ����   | localhost��������  �� |��������������   ��                     |
    |        | ip-172-17-20-18     |                                      | 
    | dengli | %                   | 49ba59abbe56e057                     |  �� root���뱻���� 
    +------+-----------------------+--------------------------------------+ 
    
    6 row in set (0.01 sec)
    
    mysql> exit�� �� �˳�MySQL������
    
    Bye
    
    [root@centos ~]# mysql -u centospub -p�� �� ���½�����dengli�û���¼MySQL������
    Enter password:�� �� ��������������
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
 * ����4:�������Ӷ˿ڣ�3306��
  ```html
  [root@centos ~]# vim /etc/sysconfig/iptables
  ```
 ��iptables���ļ������ -I INPUT -p tcp --dport 3306 -j ACCEPT��Ȼ�󱣴��˳���

 �����Ѿ�����˴MySQL���ݿ��������   
  






