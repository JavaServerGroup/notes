docker 1.12之后新增了一个swarm mode被集成在docker engine中，安装docker后就可以被使用，且不需要额外的key-value存储和负载均衡器。

swarm mode分为四个部分，

    docker swarm
    docker node
    docker service
    docker stack/deploy //暂不介绍
下面分别说明
#docker swarm
    docker swarm --help
    Usage:  docker swarm COMMAND
        
    Manage Docker Swarm
    
    Options:
              --help   Print usage
    
    Commands:
        init        Initialize a swarm
        join        Join a swarm as a node and/or manager
        join-token  Manage join tokens
        update      Update the swarm
        leave       Leave a swarm
##init
    docker swarm init --help
    Usage:  docker swarm init [OPTIONS]
    
    Initialize a swarm
    
    Options:
        --advertise-addr string           Advertised address (format: <ip|interface>[:port])
        
        --cert-expiry duration            Validity period for node certificates (default 2160h0m0s)
        
        --dispatcher-heartbeat duration   Dispatcher heartbeat period (default 5s)
        
        --external-ca value               Specifications of one or more certificate signing endpoints
        
        --force-new-cluster               Force create a new cluster from current state.
        
        --help                            Print usage
        
        --listen-addr value               Listen address (format: <ip|interface>[:port]) (default 0.0.0.0:2377)
        
        --task-history-limit int          Task history retention limit (default 5)

创建一个swarm集群，主要参数为`--listen-addr`

    docker swarm init --listen-addr 172.25.10.12:2377
表示监听172.25.10.12的2377端口

##join
    docker swarm join --help
    Usage:  docker swarm join [OPTIONS] HOST:PORT
    
    Join a swarm as a node and/or manager
    
    Options:
      --advertise-addr string   Advertised address (format: <ip|interface>[:port])
      --help                    Print usage
      --listen-addr value       Listen address (format: <ip|interface>[:port]) (default 0.0.0.0:2377)
      --token string            Token for entry into the swarm

join 一个swarm集群，主要参数为`--token`，token会决定该宿主机加入集群的身份，是manager还是worker
ps: swarm集群中分为两个角色，manager和worker，其中manager可以管理集群，worker不可以管理集群，一个swarm集群中可以有多个manager，其中会有一个被推举为leader，如果leader挂了，则其余的manager会再次推举一个leader出来，但是该集群中必须有三个或三个以上manager才可以，也就是说如果只有有2个manager的话，leader挂了，另外一个也会挂掉。

    docker swarm join --token SWMTKN-1-54yizc39q5il3qxl4pflpmnukhj48k3yxiicsr4vr8rbjv2uvp-0k55cexwskzm2w9dqgnl5gmdu 172.25.10.12:2377
表示加入172.25.10.12的swarm集群，token表示是worker身份，token可以用`docker swarm join-token worker/manager`查询

##join-token
    docker swarm join-token --help
    Usage:  docker swarm join-token [-q] [--rotate] (worker|manager)
    
    Manage join tokens
    
    Options:
          --help     Print usage
     -q,  --quiet    Only display token
          --rotate   Rotate join token
主要用法为

    docker swarm join-token worker
    docker swarm join-token manager

##update
    docker swarm update --help
    Usage:  docker swarm update [OPTIONS]
    
    Update the swarm
    
    Options:
      --cert-expiry duration            Validity period for node certificates (default 2160h0m0s)
      --dispatcher-heartbeat duration   Dispatcher heartbeat period (default 5s)
      --external-ca value               Specifications of one or more certificate signing endpoints
      --help                            Print usage
      --task-history-limit int          Task history retention limit (default 5)
更新swarm集群

##leave
    docker swarm leave --help
    Usage:  docker swarm leave [OPTIONS]
    
    Leave a swarm
    
    Options:
      --force   Force leave ignoring warnings.
      --help    Print usage
离开集群，如果为manager，可以用`--force`强制脱离

*以下命令只能在manager上面使用*
#docker node
    docker node --help
    Usage:  docker node COMMAND
    
    Manage Docker Swarm nodes
    
    Options:
      --help   Print usage
    
    Commands:
      demote      Demote a node from manager in the swarm
      inspect     Display detailed information on one or more nodes
      ls          List nodes in the swarm
      promote     Promote a node to a manager in the swarm
      rm          Remove a node from the swarm
      ps          List tasks running on a node
      update      Update a node
##ls
    docker node ls --help
    Usage:  docker node ls [OPTIONS]
    List nodes in the swarm
    Aliases:
      ls, list
    Options:
      -f, --filter value   Filter output based on conditions provided
          --help           Print usage
      -q, --quiet          Only display IDs
##demote
    docker demote --help
    Usage:  docker node demote NODE [NODE...]
    
    Demote a node from manager in the swarm
    
    Options:
          --help   Print usage
##inspect
    docker node inspect --help
    Usage:  docker node inspect [OPTIONS] self|NODE [NODE...]
    Display detailed information on one or more nodes
    Options:
      -f, --format string   Format the output using the given go template
          --help            Print usage
          --pretty          Print the information in a human friendly format.
##promote
    docker node promote --help
    Usage:  docker node promote NODE [NODE...]
    Promote a node to a manager in the swarm
    Options:
          --help   Print usage
##rm
    docker node rm --help
    Usage:  docker node rm NODE [NODE...]
    Remove a node from the swarm
    Aliases:
      rm, remove
    Options:
          --help   Print usage
##ps
    docker node ps --help
    Usage:  docker node ps [OPTIONS] self|NODE
    List tasks running on a node
    Options:
      -f, --filter value   Filter output based on conditions provided
          --help           Print usage
          --no-resolve     Do not map IDs to Names
##update
    docker node update --help
    Usage:  docker node update [OPTIONS] NODE
    Update a node
    Options:
          --availability string   Availability of the node (active/pause/drain)
          --help                  Print usage
          --label-add value       Add or update a node label (key=value) (default [])
          --label-rm value        Remove a node label if exists (default [])
          --role string           Role of the node (worker/manager)

#docker service
    docker service --help
    Usage:  docker service COMMAND
    Manage Docker services
    Options:
          --help   Print usage
    Commands:
      create      Create a new service
      inspect     Display detailed information on one or more services
      ps          List the tasks of a service
      ls          List services
      rm          Remove a service
      scale       Scale one or multiple services
      update      Update a service
##create
    docker service create --help
    Usage:  docker service create [OPTIONS] IMAGE [COMMAND] [ARG...]
    Create a new service
    Options:
          --constraint value               Placement constraints (default [])
          --container-label value          Container labels (default [])
          --endpoint-mode string           Endpoint mode (vip or dnsrr)
      -e, --env value                      Set environment variables (default [])
          --help                           Print usage
      -l, --label value                    Service labels (default [])
          --limit-cpu value                Limit CPUs (default 0.000)
          --limit-memory value             Limit Memory (default 0 B)
          --log-driver string              Logging driver for service
          --log-opt value                  Logging driver options (default [])
          --mode string                    Service mode (replicated or global) (default "replicated")
          --mount value                    Attach a mount to the service
          --name string                    Service name
          --network value                  Network attachments (default [])
      -p, --publish value                  Publish a port as a node port (default [])
          --replicas value                 Number of tasks (default none)
          --reserve-cpu value              Reserve CPUs (default 0.000)
          --reserve-memory value           Reserve Memory (default 0 B)
          --restart-condition string       Restart when condition is met (none, on-failure, or any)
          --restart-delay value            Delay between restart attempts (default none)
          --restart-max-attempts value     Maximum number of restarts before giving up (default none)
          --restart-window value           Window used to evaluate the restart policy (default none)
          --stop-grace-period value        Time to wait before force killing a container (default none)
          --update-delay duration          Delay between updates
          --update-failure-action string   Action on update failure (pause|continue) (default "pause")
          --update-parallelism uint        Maximum number of tasks updated simultaneously (0 to update all at once) (default 1)
      -u, --user string                    Username or UID
          --with-registry-auth             Send registry authentication details to swarm agents
      -w, --workdir string                 Working directory inside the container

具体参数可以去这里面查[https://docs.docker.com/engine/swarm/services/](https://docs.docker.com/engine/swarm/services/)
这里主要说一下现在有用到的命令

    docker service create --replicas 2 --name shakeshakeapp --network=shake-net -p 8080:8080 --mount type=bind,src=/var/lib/docker/workspace/shakeshake/webapps,dst=/opt/app/tomcat/webapps myregistrydomain.com:5000/wen/tomcat:v8

`--replicas`表示创建副服务数目，这里创建2个服务，
`--name`表示应用在overlay网络中的名称（关于overlay网络参考docker network）.
`-p`表示映射服务容器的端口8080到外部宿主机8080端口，
`--mount`表示挂载卷到服务，type有两种，volume,bind：`docker service create --mount type=volume,src=<VOLUME-NAME>,dst=<CONTAINER-PATH>,volume-driver=<DRIVER>,volume-opt=<KEY0>=<VALUE0>,volume-opt=<KEY1>=<VALUE1>`创建一个volume`docker service create --mount src=<VOLUME-NAME>,dst=<CONTAINER-PATH>`挂载一个已经存在的volume`docker service create --mount type=bind,src=<HOST-PATH>,dst=<CONTAINER-PATH>`挂载一个data volume
最后一个是使用的镜像，这里使用本地仓库的镜像

##inspect
    docker service inspect --help
    Usage:  docker service inspect [OPTIONS] SERVICE [SERVICE...]
    Display detailed information on one or more services
    Options:
      -f, --format string   Format the output using the given go template
          --help            Print usage
          --pretty          Print the information in a human friendly format.
##ps
    docker service ps --help
    Usage:  docker service ps [OPTIONS] SERVICE
    List the tasks of a service
    Options:
      -f, --filter value   Filter output based on conditions provided
          --help           Print usage
          --no-resolve     Do not map IDs to Names
##ls
    docker service ls --help
    Usage:  docker service ls [OPTIONS]
    List services
    Aliases:
      ls, list
    Options:
      -f, --filter value   Filter output based on conditions provided
          --help           Print usage
      -q, --quiet          Only display IDs
##rm
    docker service rm --help
    Usage:  docker service rm [OPTIONS] SERVICE [SERVICE...]
    Remove a service
    Aliases:
      rm, remove
    Options:
          --help   Print usage
##scale
    docker service scale --help
    Usage:  docker service scale SERVICE=REPLICAS [SERVICE=REPLICAS...]
    Scale one or multiple services
    Options:
          --help   Print usage
用法:

    docker service scale shakeshakeapp=3//设置服务数为3个
    docker service scale shakeshakeapp=5//设置服务数为5个
##update
    docker service update --help
    Usage:  docker service update [OPTIONS] SERVICE
    Update a service
    Options:
          --args string                    Service command args
          --constraint-add value           Add or update placement constraints (default [])
          --constraint-rm value            Remove a constraint (default [])
          --container-label-add value      Add or update container labels (default [])
          --container-label-rm value       Remove a container label by its key (default [])
          --endpoint-mode string           Endpoint mode (vip or dnsrr)
          --env-add value                  Add or update environment variables (default [])
          --env-rm value                   Remove an environment variable (default [])
          --help                           Print usage
          --image string                   Service image tag
          --label-add value                Add or update service labels (default [])
          --label-rm value                 Remove a label by its key (default [])
          --limit-cpu value                Limit CPUs (default 0.000)
          --limit-memory value             Limit Memory (default 0 B)
          --log-driver string              Logging driver for service
          --log-opt value                  Logging driver options (default [])
          --mount-add value                Add or update a mount on a service
          --mount-rm value                 Remove a mount by its target path (default [])
          --name string                    Service name
          --network-add value              Add or update network attachments (default [])
          --network-rm value               Remove a network by name (default [])
          --publish-add value              Add or update a published port (default [])
          --publish-rm value               Remove a published port by its target port (default [])
          --replicas value                 Number of tasks (default none)
          --reserve-cpu value              Reserve CPUs (default 0.000)
          --reserve-memory value           Reserve Memory (default 0 B)
          --restart-condition string       Restart when condition is met (none, on-failure, or any)
          --restart-delay value            Delay between restart attempts (default none)
          --restart-max-attempts value     Maximum number of restarts before giving up (default none)
          --restart-window value           Window used to evaluate the restart policy (default none)
          --stop-grace-period value        Time to wait before force killing a container (default none)
          --update-delay duration          Delay between updates
          --update-failure-action string   Action on update failure (pause|continue) (default "pause")
          --update-parallelism uint        Maximum number of tasks updated simultaneously (0 to update all at once) (default 1)
      -u, --user string                    Username or UID
          --with-registry-auth             Send registry authentication details to swarm agents
      -w, --workdir string                 Working directory inside the container

更新一些service的相关配置，需要注意的是以下三个参数

    --update-delay duration          Delay between updates
    --update-failure-action string   Action on update failure (pause|continue) (default "pause")
    --update-parallelism uint        Maximum number of tasks updated simultaneously (0 to update all at once) (default 1)
官网解释如下：
>The --update-delay flag configures the time delay between updates to a service task or sets of tasks. You can describe the time T as a combination of the number of seconds Ts, minutes Tm, or hours Th. So 10m30s indicates a 10 minute 30 second delay.

>By default the scheduler updates 1 task at a time. You can pass the --update-parallelism flag to configure the maximum number of service tasks that the scheduler updates simultaneously.

>When an update to an individual task returns a state of RUNNING, the scheduler continues the update by continuing to another task until all tasks are updated. If, at any time during an update a task returns FAILED, the scheduler pauses the update. You can control the behavior using the --update-failure-action flag for docker service create or docker service update.
