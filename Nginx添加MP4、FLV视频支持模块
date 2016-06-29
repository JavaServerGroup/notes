

由于公司配置nginx默认是没有编译MP4支持模块，于是尝试重新编译添加MP4支持模块，由于各种原因，原有nginx不建议重新增加模块，于是
我重新又开了一个nginx进程，步骤如下：

第一步，下载安装nginx，命令是
    # wget http://nginx.org/download/nginx-1.10.1.tar.gz  -->建议下载nginx最新稳定版
    # tar -zxvf nginx-1.10.1.tar.gz        -->解压
    # cd nginx-1.10.1
    # ./configure                       -->编译
      --prefix=/DISKB/nginx             -->设置nginx的安装目录 
      --with-http_ssl_module 
      --with-http_realip_module  
      --with-http_sub_module 
      --with-http_stub_status_module 
      --with-pcre=/opt/pcre-8.32 
      --with-zlib=/opt/zlib-1.2.8 
      --with-openssl=/opt/zlib-1.2.8
      --with-ngx_http_core_module
      --with-http_mp4_module              -->MP4视频支持模块
      --with-http_flv_module              -->FLV视频支持模块
    # make && make install   -->此步骤最容易出错，通常是由于pcre，zlib，openssl模块没有正确编译，报错通常是/opt/pcre-8.32，
    /opt/zlib-1.2.8，/opt/zlib-1.2.8 目录不存在，于是转而安装pcre，zlib，openssl，步骤请参考
    https://github.com/JavaServerGroup/notes/blob/master/Nginx%E6%90%AD%E5%BB%BA.md#nginx的安装和启动 

第二步，修改配置文件&防火墙，命令是
    # cd /DISKB/nginx/conf/
    # vim nginx.conf   -->下面只列出http段，而且只要修改该段就行
    ...
    http {
        include       mime.types;
        default_type  application/octet-stream;

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '  -->放开日志
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';

        #access_log  logs/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        #keepalive_timeout  0;
        keepalive_timeout  65;

        #gzip  on;

        server {
            listen       8081;      -->默认端口是80，此处要修改端口为8081，为避免开第二个nginx端口和第一个nginx重复
            server_name  localhost;

            #charset koi8-r;

            access_log  /DISKB/nginx/logs/host.access.log  main;      -->修改access日志存放路径

           location / {
                root   html;     -->此处是MP4，FLV视频存放路径，可以修改为自己想要的路径
                index  index.html index.htm;
                limit_rate_after 10m;     -->设置http请求传输多少字节后开始限速，依赖于ngx_http_core_module模块
                limit_rate 150K;    -->控制速度，依赖于ngx_http_core_module模块
            }

            location ~ \.flv$ {          -->添加支持flv视频location段
                flv;
            }
            location ~ \.mp4$ {          -->添加支持MP4视频location段
                mp4;
                mp4_buffer_size   1m;
                mp4_max_buffer_size   5m;
            }
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        }
    ...
    保存，退出，之后便是修改防火墙，开放8081端口，命令是
    # vim /etc/sysconfig/iptables      -->添加-A INPUT -p tcp -m tcp --dport 8081 -j ACCEPT，保存退出
    # service /etc/sysconfig/iptables restart  -->重启防火墙
    
第三步，启动nginx
    # /DISKB/nginx/sbin/nginx -c /DISKB/nginx/conf/nginx.conf -t -->检验配置文件的正确性
    # /DISKB/nginx/sbin/nginx -c /DISKB/nginx/conf/nginx.conf    -->启动nginx，建议采用绝对路径启动
第四步，检验是否配置成功
    将.mp4、.flv后缀名的文件放到相应的目录下，再去浏览器访问，访问路径是http://ip:8081/mp4/test.mp4，
    我这里只验证了MP4视频，如果能正确访，问则表明配置成功，命令是
    # cd /DISKB/nginx/html/
    # mkdir mp4 flv
    # cp /home/www/test.mp4 mp4
    # cp /home/www/test.flv flv
    
    
    
