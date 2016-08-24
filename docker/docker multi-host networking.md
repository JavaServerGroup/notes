docker多宿主机间的容器通信主要有以下三种方式。
#1.ip直连
如果容器启动时把端口暴露给外部，那么就可以通过直接访问ip+端口的形式访问容器。
#2.swarm编配工具
在docker1.11之前的版本有一个docker swarm的集群和调度工具，用consul做服务发现，把两台机器的ip注册进consul key-value集群，然后在两台宿主机上分别启动swarm容器，就可以在两台机器上进行容器互联了。
因为现在的docker版本为1.12，swarm有个新的方式，故这种方式只做简要介绍。
#3.1.12版本swarm
docker 1.12之后新增了一个swarm mode被集成在docker engine中，安装docker后就可以被使用，且不需要额外的key-value存储和负载均衡器。
下面以配置一个shakeshakeapp为例，

有两台宿主机 172.25.10.11和172.25.10.12，在上面分别装有docker-engine,开放端口2377 tcp端口, 7946 4789 tcp udp 端口

172.25.10.12上执行

    docker swarm init --listen-addr 172.25.10.12:2377

172.25.10.11上执行

    docker swarm join --token SWMTKN-1-54yizc39q5il3qxl4pflpmnukhj48k3yxiicsr4vr8rbjv2uvp-0k55cexwskzm2w9dqgnl5gmdu 172.25.10.12:2377

10.11作为worker加入集群管理后所有的操作都可以在12上执行了

    docker network create -driver overlay shake-net //创建overlay网络
    
    docker service create --replicas 1 --name redis --network=shake-net myregistrydomain.com:5000/wen/redis:v3.0 //以本地仓库的镜像创建一个服务
    
    docker service create --replicas 2 --name shakeshakeapp --network=shake-net -p 8080:8080 --mount type=bind,src=/var/lib/docker/workspace/shakeshake/webapps,dst=/opt/app/tomcat/webapps myregistrydomain.com:5000/wen/tomcat:v8 //创建2个shake服务连接同一个redis，并且挂载本地卷到容器上，绑定端口8080到8080

这样就创建好了一个shakeshake集群，只要访问172.25.10.12:8080端口的shake服务即可，swarm自己可以管理负载均衡。

swarm具体参数配置详解请见docker swarm 1.12
私有仓库配置见Docker私有仓库与认证管理
