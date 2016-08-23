###CentOS系统时间与UTC时间不一致的解决方法

---

#####【知识点】

######1.CST：
中国标准时间（China Standard Time），这个解释可能是针对RedHat Linux。
       
       
######2.UTC：
  协调世界时，又称世界标准时间，简称UTC，从英文国际时间/法文协调时间 "Universal Time/Temps Cordonné" 而来，
中国大陆、香港、澳门、台湾、蒙古国、新加坡、马来西亚、菲律宾、澳洲西部的时间与UTC的时差均为+8，也就是UTC+8。
        
######3.NTP：
Network Time Protocol简称（ntp），是一个能使计算机之间实现时间同步的协议，发布这个校准时间的服务器就是NTP服务器。
           
#####【解决方案】

#####前提 1: 如果没有安装ntp服务器，请先执行安装ntp的命令

    # yum install -y ntp 
    
#####前提 2: 网络时间服务器地址
以下一些国内可用的时间服务器：

     1.cn.pool.ntp.org
     2.cn.pool.ntp.org
     3.cn.pool.ntp.org
     0.cn.pool.ntp.org
     cn.pool.ntp.org
     tw.pool.ntp.org
     0.tw.pool.ntp.org
     1.tw.pool.ntp.org
     2.tw.pool.ntp.org
     3.tw.pool.ntp.org
当然还有国内常用NTP服务器地址及IP：
    
     210.72.145.44  (国家授时中心服务器IP地址)
     133.100.11.8  日本 福冈大学
     time-a.nist.gov 129.6.15.28 NIST, Gaithersburg, Maryland 
     time-b.nist.gov 129.6.15.29 NIST, Gaithersburg, Maryland 
     time-a.timefreq.bldrdoc.gov 132.163.4.101 NIST, Boulder, Colorado 
     time-b.timefreq.bldrdoc.gov 132.163.4.102 NIST, Boulder, Colorado 
     time-c.timefreq.bldrdoc.gov 132.163.4.103 NIST, Boulder, Colorado 
     utcnist.colorado.edu 128.138.140.44 University of Colorado, Boulder 
     time.nist.gov 192.43.244.18 NCAR, Boulder, Colorado 
     time-nw.nist.gov 131.107.1.10 Microsoft, Redmond, Washington 
     nist1.symmetricom.com 69.25.96.13 Symmetricom, San Jose, California 
     nist1-dc.glassey.com 216.200.93.8 Abovenet, Virginia 
     nist1-ny.glassey.com 208.184.49.9 Abovenet, New York City 
     nist1-sj.glassey.com 207.126.98.204 Abovenet, San Jose, California 
     nist1.aol-ca.truetime.com 207.200.81.113 TrueTime, AOL facility, Sunnyvale, California 
     nist1.aol-va.truetime.com 64.236.96.53 TrueTime, AOL facility, Virginia

     ntp.sjtu.edu.cn 202.120.2.101 (上海交通大学网络中心NTP服务器地址）
     s1a.time.edu.cn     北京邮电大学
     s1b.time.edu.cn	    清华大学
     s1c.time.edu.cn	    北京大学
     s1d.time.edu.cn	    东南大学
     s1e.time.edu.cn	    清华大学
     s2a.time.edu.cn	    清华大学
     s2b.time.edu.cn	    清华大学
     s2c.time.edu.cn	    北京邮电大学
     s2d.time.edu.cn	    西南地区网络中心
     s2e.time.edu.cn	    西北地区网络中心
     s2f.time.edu.cn	    东北地区网络中心
     s2g.time.edu.cn	    华东南地区网络中心
     s2h.time.edu.cn	    四川大学网络管理中心
     s2j.time.edu.cn	    大连理工大学网络中心
     s2k.time.edu.cn     CERNET桂林主节点
     s2m.time.edu.cn     北京大学
     
##### 步骤:
 
1.查看CentOS当前时区，发现不是UTC时区：
    # date
    
        Mon Aug 22 05:55:22 EDT 2016
2.在CentOS中执行命令，修改/etc/sysconfig/clock配置文件
    # vim /etc/sysconfig/clock
    
        #The timezone of the system is defined by the contents of /etc/localtime.
            ZONE="America/New_York"
            UTC=true
            ARC=false
对此，我的建议是，改为如下内容：

        #The timezone of the system is defined by the contents of /etc/localtime.
            ZONE="UTC"
            UTC=true
            
   保存，退出，执行命令：
   
    :wq
   
3.建立软连接，将CentOS的时区设置为UTC时区，命令是:
    # ln -sf /usr/share/zoneinfo/UTC /etc/localtime
    
4.对准时间，命令是：
    # ntpdate cn.pool.ntp.org
    
5.设置硬件时间和系统时间一致并校准，命令是：
    # /sbin/hwclock --systohc
    
6.建议在CentOS中设置定时同步时区的任务
     # crontab -e
     
添加命令，目的是每天早上六点执行同步时区的任务，在logger -t NTP中，logger 是一个shell 命令接口，可以通过该接口使用Syslog的系统日志模块，还可以从命令行直接向系统日志文件写入一行信息，-t NTP 每行记录都加上“NTP”这个标签

    0 6 * * * /usr/sbin/ntpdate cn.pool.ntp.org | logger -t NTP

保存，退出，执行命令：
   
    :wq
    
