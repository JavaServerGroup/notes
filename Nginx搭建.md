##搭建Nginx





#####【知识目标】

1. <a href="#Nginx基本概念">Nginx基本概念</a>
2. <a href="#Nginx的优点">Nginx的优点</a>
3. <a href="#Nginx的安装">Nginx的安装</a>
4. <a href="#Nginx的配置">Nginx的配置</a>


#####【<a name="Nginx基本概念" id="Nginx基本概念" ><font color=black>Nginx基本概念</font></a>】

Nginx(发音同engine x)是一款[轻量级](http://baike.baidu.com/link?url=Cex29kJQgnrZOovNVf_xVPOoa-oiXs_H3ZjDNhqE-u3e8KwiLd8XkTXC7MQ5qcjKxpMd5muUodtFv89TSoaW6a)的Web 服务器/反向代理服务器及电子邮件（[IMAP](http://baike.baidu.com/link?url=6SVN81vjf_rMrr0tHK9TExgGLPtfHDF-nzUlHb0AZ3AwWeyDmfEQ1OMlpOWxDI_ilwwVeI3KE-tPUfIkI4UrlK)/[POP3](http://baike.baidu.com/link?url=VIO3_nO9yW9JcNF4BbQPYsses6B1UU7EiDODvFHDU3nhGbTz9vGs6KZTuAGFvYrFrzKFFe0zUPl0FGNB5io70K)）代理服务器，并在一[个BSD-like](http://repoze.org/license.html) 协议下发行。由俄罗斯的程序设计师[Igor Sysoev](https://en.wikipedia.org/wiki/Igor_Sysoev)所开发，供俄国大型的入口网
站及搜索引擎Rambler（俄文：Рамблер）使用。其特点是占有内存少，[并发](http://baike.baidu.com/link?url=OM0t4QSPd6ZXhwtXhw3RLeJqN1K9Ujpt9RKxpMFPCngrTLiR7q5Ywv9nUiRRwbb_mx4NjpZwOVfUrGnR_919Rq)能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、新浪、网易、腾
讯等。(来自：百度百科)

#####【<a name="Nginx的优点" id="Nginx的优点"><font color=black>Nginx的优点</font></a>】   

  * Nginx 可以在[UNIX](http://www.unix.org/)、[GNU](http://www.gnu.org/)/[Linux](http://www.linux.org/)、BSD、[Mac OS X](http://www.iplaysoft.com/osx-yosemite.html)、[Solaris](http://www.oracle.com/technetwork/server-storage/solaris11/downloads/index.html)，以及[Microsoft Windows](http://www.microsoft.com/zh-cn)等操作系统中运行；
 
* 在高连接并发的情况下，Nginx是Apache服务器不错的替代品，能够支持高达 50,000 个并发连接数的响应；
  
* Nginx作为[负载均衡](http://baike.baidu.com/link?url=TwmFOgB_mGNM395S-n3tNwN55DylIqZi7_bVxNivF5hdsCdCQrnHQS71N9yYfn8ROSRkDVnTnOQjz9vhHzXOYq)服务器：Nginx 既可以在内部直接支持 [Rails](http://rubyonrails.org/) 和 [PHP](http://www.php.net/) 程序对外进行服务，也可以支持作为 HTTP代理服务器对外进行服务。Nginx采用C进行编写，不论是系统资源开销还是CPU使用效率都比
  
* Nginx代码完全用C语言从头写成，已经移植到许多体系结构和操作系统，包括：Linux、[FreeBSD](http://www.freebsd.org/)、Solaris、Mac OS X、AIX以及Microsoft Windows。Nginx有自己的函数库，并且除了[zlib](http://www.zlib.net/)、[PCRE](http://sourceforge.net/projects/pcre/files/)和[OpenSSL](https://www.openssl.org/source/)
    之外，标准模块只使用系统C库函数。而且，如果不需要或者考虑到潜在的授权冲突，可以不使用这些第三方库；
  
* 作为邮件代理服务器：Nginx 同时也是一个非常优秀的邮件代理服务器（最早开发这个产品的目的之一也是作为邮件代理服务器），Last.fm 描述了成功并且美妙的使用经验；
  
* Nginx 是一个安装非常的简单、配置文件非常简洁（还能够支持perl语法）、Bugs非常少的服务器。Nginx 启动特别容易，并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够不间断
    服务的情况下进行软件版本的升级。(来自：百度百科)
 

#####【<a name="Nginx的安装和启动" id="Nginx的安装和启动"><font color=black>Nginx的安装和启动</font></a>】
方法一：源码编译安装依赖库+源码编译安装nginx，不建议yum安装nginx，不然会为后续的配置带来不少麻烦
    下载并安装所需要的依赖库，依次是：gcc（一般的机器都已经安装gcc）、openssl、zlib、pcre

* 步骤1：gcc安装，gcc没安装的话不能进行源码编译安装，所以得先确认服务器有没有安装gcc，命令是rpm -qa|grep gcc,无显示内容则表示没有安装，而对于安装gcc来说，不同的系统命令不同，Ubuntu系统的
     命令是apt-get install gcc，而centOS系统的命令是yum install gcc，这里建议加入参数-y，命令是：yum -y install gcc，意思是自动选择y安装

* 步骤2：openssl安装，下载openssl，地址是https://www.openssl.org/source/，然后选择适当的版本，或者直接wget https://www.openssl.org/source/openssl-fips-2.0.10.tar.gz
        [root@localhost mrms]# tar -zxvf openssl-fips-2.0.10.tar.gz //解压缩
        [root@localhost mrms]# cd openssl-fips-2.0.10  //切换目录
        [root@localhost openssl-fips-2.0.10]# ./config //配置
        [root@localhost openssl-fips-2.0.10]# make && make install //安装
            
* 步骤3：zlib安装，下载zlib，地址是http://www.zlib.net/ ，然后选择适当的版本，或者直接wget http://www.zlib.net/zlib-1.2.8.tar.gz
        [root@localhost mrms]# tar -zxvf zlib-1.2.8.tar.gz //解压缩
        [root@localhost mrms]# cd zlib-1.2.8  //切换目录
        [root@localhost zlib-1.2.8]# ./configure //配置
        [root@localhost zlib-1.2.8]# make && make install //安装

* 步骤4：pcre安装，下载pcre，地址是http://sourceforge.net/projects/pcre/files/，然后选择适当的版本，或者直接wget http://sourceforge.net/projects/pcre/files/pcre/8.37/pcre-8.37.tar.gz
        [root@localhost mrms]# tar -zxvf pcre-8.37.tar.gz  //解压缩
        [root@localhost mrms]# cd pcre-8.37  //切换目录
        [root@localhost pcre-8.37]# ./configure  //配置
        [root@localhost pcre-8.37]# make && make install //安装
* 步骤5：nginx安装，下载nginx ，地址是http://nginx.org/en/download.html，然后选择适当的版本，或者直接wget http://nginx.org/download/nginx-1.9.6.tar.gz
        [root@localhost mrms]# tar -zxvf nginx-1.9.6.tar.gz //解压缩
        [root@localhost mrms]# cd nginx-1.9.6 //切换目录
        [root@localhost nginx-1.9.6]# ./configure --prefix=/userdata1/nginx --with-openssl=../openssl --with-zlib=../zlib --with-pcre=../pcre //配置
        [root@localhost nginx-1.9.6]# make && make install //安装
    切记编译安装Nginx时，命令./configure 后面带的参数很重要，建议像我这样加上比较好。

* 步骤6：检查nginx是否安装成功
        [root@localhost nginx-1.9.6]# ./../../nginx/sbin/nginx -t
            nginx: the configuration file /userdata1/nginx/conf/nginx.conf syntax is ok
            nginx: configuration file /userdata1/nginx/conf/nginx.conf test is successful
    至此Nginx安装完成！

* 步骤7：启动nginx
        [root@localhost nginx-1.9.6]# cd /userdata1/nginx/sbin/
        [root@localhost sbin]# ./nginx
    
* 步骤8：检查nginx是否启动
        [root@localhost sbin]# netstat -ntlp
         Proto Recv-Q Send-Q Local Address     Foreign Address           State       PID/Program name
         tcp        0      0 127.0.0.1:3306      0.0.0.0:*               LISTEN      1329/mysqld     
         tcp        0      0 0.0.0.0:80          0.0.0.0:*               LISTEN      32129/nginx     
         tcp        0      0 127.0.1.1:53        0.0.0.0:*               LISTEN      1336/dnsmasq    
         tcp        0      0 127.0.0.1:631       0.0.0.0:*               LISTEN      7098/cupsd      
         tcp6       0      0 ::1:631                  :::*               LISTEN      7098/cupsd 
    
#####【<a name="Nginx的配置" id="Nginx的配置"><font color=black>Nginx的配置</font></a>】   

 因为工作安排，我这里需要搭建一个Nginx(Web Server)+Tomcat+DB(MySql)(db已经搭建)的环境，nginx监听本机的tomcat，所以需要在配置文件中修改一些内容，
   
* 步骤1：打开/userdata1/nginx/conf目录下的nginx.conf，需要配置的内容是
       (1)添加upstream，具体是:
          upstream hostname{
               server 127.0.0.1:8080；
         }
       (2)添加location，具体是：
          location /{
                 proxy_pass http://hostname;
                 proxy_set_header X-Real-IP $remote_addr;
         }
 保存退出。
* 步骤2：重启nginx
        [root@localhost conf]# /userdata1/nginx/sbin -t  //检验修改处是否正确符合规定，若未提示错误，即可重启nginx
        [root@localhost conf]# /userdata1/nginx/sbin -s reload  //重启nginx
 

