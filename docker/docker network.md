#docker network
容器之间的连接用网络创建，这被称为docker networking。docker networking允许用户创建自己的网络，容器可以通过这个网上互相通信，以补充了现有的docker0,也可以跨越不同的主机进行容器间的通信(overlay 网络见multi-host networking)

#sample example
    docker network create app // 创建网络 名字是app
    docker run -d --net=app --name db redis // 创建redis容器，接入app
    //修改应用连接redis的ip，改为db
    docker run -d --net=app --name shake shake // 创建shake容器，接入app
    docker network connect app db2 //接入已经存在的容器
    docker network disconnect app db2 //断开容器

#command 
##docker network create
    Usage:  docker network create [OPTIONS]
    Create a network
    Options:
          --aux-address value    Auxiliary IPv4 or IPv6 addresses used by Network
                                 driver (default map[])
      -d, --driver string        Driver to manage the Network (default "bridge")
          --gateway value        IPv4 or IPv6 Gateway for the master subnet (default [])
          --help                 Print usage
          --internal             Restrict external access to the network
          --ip-range value       Allocate container ip from a sub-range (default [])
          --ipam-driver string   IP Address Management Driver (default "default")
          --ipam-opt value       Set IPAM driver specific options (default map[])
          --ipv6                 Enable IPv6 networking
          --label value          Set metadata on a network (default [])
      -o, --opt value            Set driver specific options (default map[])
          --subnet value         Subnet in CIDR format that represents a
                                 network segment (default [])

|        Option    |    Equivalent    |    Description    |
|    :------: |    :-------:    |    :---------:   |
|    com.docker.network.bridge.name    |    -    |    bridge name to be used when creating the Linux bridge    |
|    com.docker.network.bridge.enable_ip_masquerade    |    --ip-masq    |    Enable IP masquerading    |
|    com.docker.network.bridge.enable_icc    |    --icc    |    Enable or Disable Inter Container Connectivity    |
|    com.docker.network.bridge.host_binding_ipv4    |    --ip    |    Default IP when binding container ports    |
|    com.docker.network.mtu    |    --mtu    |    Set the containers network MTU    |

For example, let’s use -o or --opt options to specify an IP address binding when publishing ports:

    $ docker network create \
    -o "com.docker.network.bridge.host_binding_ipv4"="172.19.0.1" \
    simple-network


##docker network connect
    Usage:  docker network connect [OPTIONS] NETWORK CONTAINER
    Connect a container to a network
    Options:
          --alias value           Add network-scoped alias for the container (default [])
          --help                  Print usage
          --ip string             IP Address
          --ip6 string            IPv6 Address
          --link value            Add link to another container (default [])
          --link-local-ip value   Add a link-local address for the container (default [])

    docker network connect multi-host-network container1
    docker run -itd --network=multi-host-network busybox
    docker network connect --ip 10.10.36.122 multi-host-network container2
    docker network connect --link container1:c1 multi-host-network container2
    docker network connect --alias db --alias mysql multi-host-network container2
    docker network connect multi-host-network container1
    docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 multi-host-network
    docker network connect --ip 172.20.128.2 multi-host-network container2

##docker network ls
    Usage:  docker network ls [OPTIONS]
    List networks
    Aliases:
      ls, list
    Options:
      -f, --filter value   Provide filter values (i.e. 'dangling=true') (default [])
          --help           Print usage
          --no-trunc       Do not truncate the output
      -q, --quiet          Only display volume names

`--no-trunc` option to display the full network id

*filter option*

|Option|Desc|Example|
|:------:|:-------:|:---------:|
|driver|network's driver|`docker network ls --filter driver=bridge`</br>`docker network ls -f "driver=bridge"`|
|id|network’s ID|`docker network ls --filter id=63d1ff1f77b07ca51070a8c227e962238358bd310bde1529cf62e6c307ade161 // full id`</br>`docker network ls --filter id=95e74588f40d // sub id`</br>`docker network ls --filter id=95e`|
|label|The label filter matches networks based on the presence of a label alone or a label and a value.|`docker network ls -f "label=usage"`|
|name|all or part of a network’s name.|`docker network ls --filter name=foo`|
|type|The type filter supports two values, builtin displays predefined networks (bridge, none, host), whereas custom displays user defined networks.|`docker network ls --filter type=custom`|

#docker network disconnect
    Usage:  docker network disconnect [OPTIONS] NETWORK CONTAINER
    Disconnect a container from a network
    Options:
      -f, --force   Force the container to disconnect from a network
          --help    Print usage

    $ docker network disconnect multi-host-network container1
##docker network rm
    Usage:  docker network rm NETWORK [NETWORK]...
    Remove a network
    Aliases:
      rm, remove
    Options:
          --help   Print usage

    $ docker network rm my-network 3695c422697f

##docker network inspect
    Usage:  docker network inspect [OPTIONS] NETWORK [NETWORK...]
    Display detailed information on one or more networks
    Options:
      -f, --format string   Format the output using the given go template
          --help            Print usage

    docker inspect --format=''  container2 | python -m json.tool

format 用go语言形式，见[https://golang.org/pkg/text/template/](https://golang.org/pkg/text/template/)
