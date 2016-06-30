[docker 介绍](#1)  
[docker 安装](#2)  
[yum 安装](#2.1)  
[脚本安装](#2.2)  
[安装包安装](#2.3)  
[docker 进程相关命令](#3)  
[容器与镜像](#4)  

# <span id = "1">docker 介绍</span>

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）。几乎没有性能开销,可以很容易地在机器和数据中心中运行。最重要的是,他们不依赖于任何语言、框架包括系统。

# <span id = "2">docker 安装</span>

只介绍docker在centos系统中的安装，在osx和windows中需要Boot2Docker工具

**内核需要3.8或更高版本**

## <span id = "2.1">yum 安装</span>
    $ sudo yum -y install docker

## <span id = "2.2">脚本安装</span>

可以从<http://get.docker.io>获得脚本

## <span id = "2.3">安装包安装</span>

下载二进制包后即可安装

***

# <span id = "3">docker 进程相关命令</span>

    $ sudo service docker status //docker 进程状态
    $ sudo service docker start //docker 进程开启
    $ sudo service docker stop //docker 进程关闭
    $ sudo docker info //docker 进程信息

***
# <span id = "4">容器与镜像</span>

    $ sudo docker run -t -i centos /bin/bash

运行上面的命令就可以在dockerhub上面下载centos镜像并运行了,
而且可以进入镜像的bash里面和真正的centos系统一样执行命令,
如果要退出只要在bash里面输入exit就可以退出了，**但是docker容器也会停止**

我们可以同过`docker ps`来查看当前系统中正在运行的容器列表，而`docker ps -a`命令会列出所有的容器。

    $ sudo docker run --name centos_container -t -i centos /bin/bash

运行上面的命令就可以给启动的的docker容器命名。
这样在对该容器进行操作是只需要对该容器的别名进行操作就可以了。
例如启动centos_container容器。

    $ sudo docker start centos_container
    $ sudo docker restart centos_container

以下命令可以创建一个后台执行的容器, -d表示后台执行：

    $ sudo docker run --name centos_d_container -d centos /bin/sh -c "while true; do echo hello world; sleep 1; done"

以下是一些常用命令：

    //获取容器日志
    $ sudo docker logs centos_d_container
    
    //与tail -f 类似 
    $ sudo docker logs -f centos_d_container
    
    //查看容器内进程 
    $ sudo docker top centos_d_container 
    
    //在容器内执行后台任务，创建一个文件
    $ sudo docker exec -d centos_d_container touch /etc/new_config_file 
    
    //在容器内运行交互命令
    $ sudo docker exec -t -i centos_d_container /bin/bash 
    
    //查看容器， -f 后可以跟go语言模板进行信息筛选
    $ sudo docker inspect centos_d_container 
    
    //停止容器
    $ sudo docker stop centos_d_container 
    
    // --restart 设置为always，无论容器的退出代码是什么，Docker都会重启该容器， 设置为on-failure时 只有当容器的退出代码为非0时才会重启，如--restart=on-failure:5表示最多重启5次
    $ sudo docker run --restart=always --name centos_restart_container -d centos /bin/sh -c "while true; do echo hello world; sleep 1; done"
    
    // 删除容器， 运行中的容器无法删除
    $ sudo docker rm centos_d_container
    
    // 删除全部容器
    $ sudo docker rm `docker ps -a -q`


