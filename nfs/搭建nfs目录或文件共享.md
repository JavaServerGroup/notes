##一、简介
NFS，英文全称是Network File System，中文全称是网络文件系统，是FreeBSD支持的文件系统中的一种，它允许网络中的计算机之间通过TCP/IP网络共享资源，在NFS应用中，本地NFS的客户端应用可以透明的读写位于远端NFS服务器上，就像访问本地文件。
##二、组成
NFS体系至少有两部分，
一台NFS服务器和若干客户机，如图所示：

   ![pic1](/nfs/images/pic1.png "test")

客户机通过TCP/IP网络远程访问存放在NFS服务器上的数据

##三、配置
###【服务器端】

步骤如下：

####1. 安装nfs

NFS可以被视为一个RPC服务，而要启动任何一个RPC服务之前，需要做好端口的对应（映射）的工作，这个工作其实就是RPCBIND这个服务所负责的。
也就是说，在启动任何一个RPC服务之前，我们都需要启动RPCBIND才行！（注意：在CentOS的5.x以前这个软体称为端口映射，在CentOS的6.x之后才称为RPCBIND）

执行命令：

查看系统是否安装nfs服务

      [root@test]# rpm -qa | grep nfs  / rpm -qa | grep rpcbind

yum安装nfs   

     [root@test]# yum install nfs-utils -y

####2. 设置nfs

配置文件：/etc/exports

  这个文件就是NFS的主要配置文件，系统并没有预设值，所以这个档案不一定会存在，你可能必须要使用vim手动建立；

  ![pic2](/nfs/images/pic2.png "test2")

  其中允许的网段，如果设置为*，则表示允许所有网段，至于权限设置有一些常用的参数，如下：

<table>
    <thead>
        <th>参数</th>
        <th>内容说明</th>
    </thead>
    <tbody>
        <tr>
            <td>rw<br/>ro</td>
            <td>该目录分享的权限是可读写（读写）或唯读（只读），但最终能不能读写，还是与档案系统的RWX及身份有关</td>
        </tr>
        <tr>
            <td>sync<br/>async</td>
            <td>sync 表示资料会同步写入到buffer和磁盘中<br/>async 则表示资料会先暂存于buffer中，而非直接写入磁盘！</td>
        </tr>
        <tr>
            <td>no_root_squash<br/>root_squash</td>
            <td>预设的情况下，用户端 root 的身份会由 root_squash 的设定压缩成 nfsnobody，如此对伺服器的系统会较有保障。但如果你想要开放用户端使用 root 身份来操作伺服器的档案系统，那么这里就得要开 no_root_squash</td>
        </tr>
        <tr>
            <td>all_squash</td>
            <td>不论登入NFS的使用者身份是什么，用户名都会被压缩成为匿名使用者，通常也就是没有人（nfsnobody）</td>
        </tr>
        <tr>
            <td>anonuid</br>anongid</td>
            <td>匿名意指匿名（匿名者）前面关于* _squash提到的匿名使用者的UID设定值，通常为没人（nfsnobody），但是你可以自行设定这个UID的值！当然，这个UID必需要存在于你的/etc/passwd文件当中，anonuid指的是UID而anongid则是群组的GID</td>
        </tr>
    </tbody>
</table>
用户端查询服务器端分享资源的指令：/usr/sbin/showmount

这是另一个重要的NFS指令， exportfs是用在NFS服务器端，而showmount显示则主要用在客户端，showmount显示可以用来察看NFS分享出来的目录资源。

####3. 启动nfs

执行命令：

     [root@test]# /etc/init.d/rpcbind start
     [root@test]# /etc/init.d/nfs start
     [root@test]# /etc/init.d/nfslock start

注意：千万记得要安装顺序启动，先启动rpcbind，再启动nfs，并建议添加到启动项，之后便可以用service启动nfs服务，执行命令：

     [root@test]# chkconfig rpcbind on
     [root@test]# chkconfig nfs on
     [root@test]# chkconfig nfslock on

####4. 配置端口

该服务需要开启防火墙，那么到底要开启哪些端口呢，执行一条命令即可知道，执行命令：

     [root@test]# rpcinfo -p

 ![pic3](/nfs/images/pic3.png "test3")

由此可以看出需要开启的端口很多，稍稍整理之后，结论如下：

  * rpcbind启动的端口是111，同时启动在udp和tcp
  * nfs本身的服务启动在2049
  * 其他的服务是随机的，不过，建议固定端口，在/etc/sysconfig/nfs修改

执行命令：

     [root@test]# vim /etc/sysconfig/nfs
     
需要修改的文本内容如下：       

     RQUOTAD_PORT=1001

     LOCKD_TCPPORT=30001

     LOCKD_UDPPORT=30001

     MOUNTD_PORT=1002


####5. 检测nfs

在设定好nfs服务器端之后，我们可以在服务器端自我测试是否可以连线，这个时候便用到showmount命令，执行命令：

     [root@test]# showmount -e localhost

 ![pic4](/nfs/images/pic4.png "test4")

##【用户端】
作为用户端只需要开启nfs服务，然后挂载服务器端共享出来的目录即可，那么步骤如下：

1.开启nfs服务

执行命令：

     [root@test]#  /etc/init.d/rpcbind start
     [root@test]# /etc/init.d/nfs start
     [root@test]# /etc/init.d/nfslock start

注意：千万记得要安装顺序启动，先启动rpcbind，再启动nfs，并建议添加到启动项，之后便可以用service启动nfs服务，执行命令：

     [root@test]# chkconfig rpcbind on
     [root@test]# chkconfig nfs on
     [root@test]# chkconfig nfslock on

2.挂载

执行命令：

     [root@test]# mount -t nfs 172.17.30.53:/userdata1/test /userdata1/test
     [root@test]# df -h

 ![pic5](/nfs/images/pic5.png "test5")

命令df -h，可以查看是否挂载成功，如上图，红框中是挂载成功的

##【测试】
为了验证是否正确搭建nfs，我们先在服务器端共享目录新建一个文件，执行命令：

     [root@test]# vi test

写入一些文本，保存退出，

接着，去到用户端相应的目录查看是否已经有了同步过来的test文件，如果没有说明未搭建成功，请自行检查问题
我们需要尝试在用户端修改test文件，保存退出，去到服务器端查看是否修改生效，如果已经生效，那么说明双向的共享已经完成，到此nfs搭建成功!
