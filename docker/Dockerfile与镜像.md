[TOC]

#镜像

docker中的镜像是指搭载在linux虚拟化栈上的只读文件系统，简单理解就是一个应用或系统。
当容器启动是会加载这个镜像，并在这个镜像上面加载一个读写层。

##镜像相关命令

    $sudo docker images //列出本地所有镜像

**本地镜像一般保存在/var/lib/docker/garph下面，如果有失败的镜像，去这个目录下面删除文件即可**

    $sudo docker search imageName //查找镜像
    $sudo docker pull imageName //拉镜像
    $sudo docker commit imageName //上传镜像

**以上命令都是针对Docker Hub仓库而言的，以后会介绍如何搭建私有仓库**

下面介绍比较主流的构建镜像的方法：
#Dockerfile
