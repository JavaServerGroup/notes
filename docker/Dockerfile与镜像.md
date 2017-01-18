# 镜像
    docker images //查看镜像
    docker pull image //拉镜像
    docker search image //查找镜像
    docker rmi image_name //删除镜像
# Dockerfile

Dockerfile 使用基本的基于DSL语法的指令来构建Docker镜像， 之后使用docker build 命令基于dockerfile中的指令构建镜像。
    
    #Version: 0.0.1
    FROM ubuntu
    MAINTAINER James Turnbull "james@example.com"
    ENV REFRESHED_AT 2016
    RUN apt-get install -y nginx \
     && echo 'hello' \
        >/usr/share/nginx/html/index/html
    EXPOSE 80
    
    #wen/java8
    FROM ubuntu
    MAINTAINER zhanbo wen <zhanbo.wen@afmobigroup.com>
    ENV REFRESHED_AT 20160706
    ADD soft/jdk-8.gz /usr/lib/jvm
    ENV JAVA_HOME=/usr/lib/jvm/jdk1.8.0_77
    ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    ENV PATH=$PATH:$JAVA_HOME/bin
    RUN echo "JAVA_HOME=/usr/lib/jvm/jdk1.8.0_77\nCALSSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar\nPATH=$PATH:$JAVA_HOME/bin" >> /etc/profile
    
    #wen/tomcat8
    FROM wen/java8
    MAINTAINER zhanbo wen <zhanbo.wen@afmobigroup.com>
    ENV REFRESHED_AT 20160706
    ENV TOMCAT_PATH=/opt/app/tomcat
    RUN apt-get -y install wget \
     && wget http://apache.fayea.com/tomcat/tomcat-8/v8.5.11/bin/apache-tomcat-8.5.11.tar.gz \
     && tar -zxvf apache-tomcat-8.5.11.tar.gz \
     && rm -rf apache-tomcat-8.5.11.tar.gz \
     && apt-get -y autoremove wget
    RUN mv apache-tomcat-8.5.11 /opt/app/tomcat
    
    WORKDIR $TOMCAT_PATH/bin
    
    EXPOSE 8080
    ENTRYPOINT ["./catalina.sh", "run"]
    
    #tomcat8_start.sh
    sudo docker run -d -p 8080:8080 -v /var/lib/docker/workspace/shakeshake/webapps:/opt/app/tomcat/webapps --name="tomcat8" wen/tomcat8
    
    #consul
     FROM centos
     MAINTAINER zhanbo wen <zhanbo.wen@afmobi.com>
     ENV REFRESHED_AT 20170104
     ADD /consul/consul /usr/local/bin/
     ADD /consul/consul.json /config/
     ADD /consul/index.html /webui/
     ADD /consul/static /webui/static
     EXPOSE 54/udp 8300 8301 8301/udp 8302 8302/udp 8400 8500
     VOLUME ["/data"]
     ENTRYPOINT ["consul", "agent", "-config-dir=/config"]
     CMD []
     
    #consul_start.sh  
    #!/bin/bash
    sudo docker rm -f consul_test
    sudo docker run -d -p 8500:8500 -p 54:54/udp --name="consul_test" -h node1 consul_server -server -bootstrap

[dockerfile 编写建议规范](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)

以上为一个Dockerfile文件
第一行为注释
FROM表示以centos为基础镜像
MAINTAINER表示作者和联系方式
ENV表示设置环境变量REFRESHED_AT为2016,因为dockerfiler的构建是有缓存的，设置时间变量后可以改变时间变量刷新缓存
RUN表示执行的命令，也可以用数组形式表示如：RUN ["apt-get", "install", "-y", "nginx"]
EXPOSE表示开放80端口

    cd docker_file_path
    sudo docker build -t="rep_name/image_name.tag_name" .

以上为执行dockerfile的命令

# 从新镜像启动容器
    docker run -d -p 127.0.0.1:8080:80 --name cantain_name rep_name/image_name.tag_name \ nginx -g "daemon off"

-d 表示后台启动， -p 表示宿主机的127.0.0.1这个IP的8080端口映射到容器的80端口上， nginx -g "daemon off" 表示以前台方式启动nginx

    docker ps -l //查看端口映射情况
    docker port cantain_id port_num //same as before

    docker run -d -P --name cantain_name rep_name/image_name // 将EXPOSE中所有端口绑定到宿主机的49153～65535端口上

# Dockerfile 指令
## CMD
    CMD ["",""]
等同与RUN， 只能运行最后一个， 会被启动时指定指令覆盖

## ENTRYPOINT
    ENTRYPOINT [""]
类似与CMD指令，不同之处在于启动时的指定指令会被作为参数传进去
与CMD可以组合

    ENTRYPOINT ["/usr/sbin/nginx"]
    CMD ["-h"]
表示既可以默认启动后执行命令-h，也可以传入参数执行nginx命令

可以用 --entrypoint 覆盖

## WORKDIR
    WORKDIR /path //指定启动后工作目录 -w 覆盖

## ENV
    ENV PATH path //配置持久环境变量 -e 配置临时环境变量

## USER
    USER mysql //指定用户， -u 覆盖， 默认为root

## VOLUME
    VOLUME ["/opt", "/data"] //添加卷， 卷：容器间的共享目录

## ADD
    ADD file path //将构建目录下的文件或文件夹复制到镜像中的目录中 也可以URL格式

## COPY
    CPOY file path //同上，必须在dockerfile同一目录下，不会自动解压

## ONBUILD
    ONBUILD ADD . /app/src //触发器， 父镜像中的触发器会在子镜像中先被执行，子镜像的子镜像不会执行父镜像的触发器
