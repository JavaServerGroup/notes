#Docker Compose
定义一组要启动的容器，以及容器运行是的属性，这组容器叫做服务。
Docker Compose可以用一个YAML文件定义并启动服务。

Docker Compose File有两个版本，分别介绍两个版本的指令
#V1：
##build
    build: path //项目路径 绝对路径或相对路径都可以
以一个项目的dockerfile构建镜像，以这个镜像启动容器

##dockerfile
    build: .
    dockerfile: Dockerfile-alternate
替换Dockerfile启动

##cap_add, cap_drop
    cap_add:
      - ALL
    cap_drop:
      - NET_ADMIN
      - SYS_ADMIN
linux capability

##command
    command: bundle exec thin -p 3000
覆盖dockerfile中的command指令

##cgoup_parent
    cgroup_parent: m_executor-abcd
可选分组父组

##cantainer_name
    cantainer_name: test-cantainer

##devices
    devices:
      - "/dev/ttyUSB0:/dev/ttyUSB0"
挂载devices

##depends_on
    web:
      build: .
      depends_on:
        - db
        - redis
      redis:
        image: redis
      db:
        image: postgres
先启动deponds模块

##dns
    dns: 8.8.8.8
    dns:
      - 8.8.8.8
      - 9.9.9.9
设置DNS

##dns_search
    dns_search: example.com
    dns_search:
      - dc1.example.com
      - dc2.example.com
设置dns搜索域

##entrypoint
    entrypoint: /code/entrypoint.sh
    entrypoint:
      - php
      - -d
      ...
覆盖dockerfile中的entrypoint指令

##env_file
    env_file: .env
    env_file:
      - ./common.env
      - ./apps/web.env
添加环境变量，以文件形式
##environment
    environment:
      RACK_ENV: development
      SHOW: 'true'
    environment:
      - RACK_ENV=development
      - SHOW=true
设置环境变量

##expose
    expose:
      - "8080"
暴露端口

##extends
    extends:
      file: common.yml
      service: webapp
继承其他服务
可以继承common.yml中的webapp属性，如果属性唯一，例如command命令则会复写，如果属性不唯一则会 mix them

##external_links
    external_links:
      - redis_1
      - project_db_1:mysql
      - project_db_1:postgresql
链接继承的service的link

##extra_hosts
    extra_hosts:
      - "somehost:162.242.195.82"
      - "otherhost:50.31.209.229"
添加hosts

##image
    image: redis
    image: example-registry.com:4000/postgresql
从镜像启动

##labels
    labels:
      com.example.description:"Accounting webapp"
      com.example.department:"Finance"
    labels:
      - "com.example.description=Accounting webapp"

##links
    web:
      links:
        - db
        - db:database
        - redis

##log_driver&&log_opt
    log_driver:syslog
    log_opt:
      syslog-address: "tcp://192.168.0.42:123"

##net
    net: "bridge"
    net: "host"
    net: "none"
    net: "container:[service name or container name/id]"

##pid
    pid:"host"

Sets the PID mode to the host PID mode. This turns on sharing between container and the host operating system the PID address space. Containers launched with this flag will be able to access and manipulate other containers in the bare-metal machine’s namespace and vise-versa.

##ports
    ports:
      - "3000"
      - "3000-3005"
      - "8080:8080"
      - "9090-9091:8080-8081"
      - "127.0.0.1:8001:8001"
指定端口

##security_opt
    security_opt:
      - label:user:USER
      - label:role:ROLE
复写默认标签

##stop_signal
    stop_signal:SIGUSR1

##ulimits
    ulimits:
      nproc: 65535
      nofile:
       soft: 20000
       hard: 40000

## volumes, volumes_driver
    volumes:
     - /var/lib/mysql
     - /opt/data:/var/;ib/mysql
     - datavolume:/var/lib/mysql

##volumes_from
    volumes_from:
      - service_name
      - service_name:ro
      - container_name
      - container_name:rw

##cpu_shares, cpu_quota, cpuset, domainname, hostname, ipc, mac_address, mem_limit, memswap_limit, privileged, read_only, restart, shm_size, stdin_open, tty, user, working_dir
    cpu_shares: 73 //设置cpu资源分配，默认为1024
    cpu_quota: 50000 //cpu配额
    cpuset: 0,1 //指定运行的cpu， 第一个和第二个
    user: postgresql //运行时指定user
    working_dir: /code //工作目录
    domainname: foo.com //
    hostname: foo //域名
    ipc: host 
    mac_address: 02:42:ac:11:65:43
    mem_limit: 1000000000
    memswap_limit: 2000000000
    privileged: true
    restart: always
    read_only: true
    shm_size: 64M
    stdin_open: true
    tty: true

##Volume configuration
###driver
    driver:foobar
指定驱动

###driver_opts
    driver_opts:
      foo: "bar"
      baz: 1
驱动选项

###external
    services:
      db:
        image: postgres
        volumes:
          - data:/var/lib/postgres/data
    volumes:
      data:
        external: true
设置为true时，表示使用已创建的volumes, 如果volumes不存在则报错

##Network configuration
###driver
    driver: bridge || overlay //single host|| swarm

###ipam
    ipam:
      driver: default
      config:
        - subnet: 172.28.0.0/16
          ip_range: 172.28.5.0/24
          gateway: 172.28.5.254
          aux_addresses:
            host1: 172.28.1.5
            host2: 172.28.1.6
            host3: 172.28.1.7

driver: Custom IPAM driver, instead of the default.
config: A list with zero or more config blocks, each containing any of the following keys:
subnet: Subnet in CIDR format that represents a network segment
ip_range: Range of IPs from which to allocate container IPs
gateway: IPv4 or IPv6 gateway for the master subnet
aux_addresses: Auxiliary IPv4 or IPv6 addresses used by Network driver, as a mapping from hostname to IP

###external
使用已创建的网络，没有报错

##Version 1 example
    web:
      build: .
      ports:
        - "5000:5000"
      volumes:
        - .:/code
      links:
        - redis
    redis:
      image: redis

#V2
只记录与v1的不同
##context
    build：
      context： ./dir
##dockerfile
    build:
      context: .
      dockerfile: Dockerfile-alternate
##args
    build:
      args:
        buildno:1
        user: someuser
    build:
      args:
        - buildno=1
        - user=someuser
配置变量

##tmpfs
    tmpfs: /run
    tmpfs:
      - /run
      - /tmp
挂载临时文件目录

##logging
    logging:
      driver: syslog // json-file,syslog,none default json-file
      options:
        syslog-address: "tcp://192.168.0.42:123"

##network_mode
    network_mode: "bridge"
    network_mode: "host"
    network_mode: "none"
    network_mode: "service:[service name]"
    network_mode: "container:[container name/id]"

##networks
    services:
      some-service:
        networks:
          - some-network
          - other-network

##aliases
    services:
      some-services:
        networks:
          some-network:
            aliases:
              - alias1
              - alias3
          other-network:
            aliases:
              - alias2
联网时指定连接名

example
version: '2'

    services:
      web:
        build: ./web
        networks:
          - new
      worker:
        build: ./worker
        networks:
        - legacy
      db:
        image: mysql
        networks:
          new:
            aliases:
              - database
          legacy:
            aliases:
              - mysql
    networks:
      new:
      legacy:

##ipv4_address&&ipv6_address
    services:
      app:
        image: busybox
        command: ifconfig
        networks:
          app_net:
            ipv4_address:172.16.238.10
            ipv6_address:2001:3984:3989::10
指定固定ip地址

##v2 example
    version: '2'
    services:
      web:
        build: .
        ports:
          - "5000:5000"
        volumes:
          - .:/code
        networks:
          - front-tier
          - back-tier
      redis:
        image: redis
        volumes:
          - redis-data:/var/lib/redis
        networks:
          - back-tier
    volumes:
      redis-data:
        driver: local
    networks:
      front-tier:
        driver: bridge
      back-tier:
        driver: bridge

#docker compose 指令
Define and run multi-container applications with Docker.

    Usage:
      docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]
      docker-compose -h|--help

    Options:
      -f, --file FILE             Specify an alternate compose file (default: docker-compose.yml)
      -p, --project-name NAME     Specify an alternate project name (default: directory name)
      --verbose                   Show more output
      -v, --version               Print version and exit
      -H, --host HOST             Daemon socket to connect to

      --tls                       Use TLS; implied by --tlsverify
      --tlscacert CA_PATH         Trust certs signed only by this CA
      --tlscert CLIENT_CERT_PATH  Path to TLS certificate file
      --tlskey TLS_KEY_PATH       Path to TLS key file
      --tlsverify                 Use TLS and verify the remote
      --skip-hostname-check       Don't check the daemon's hostname against the name specified
                                  in the client certificate (for example if your docker host
                                  is an IP address)

    Commands:
      build              Build or rebuild services
      config             Validate and view the compose file
      create             Create services
      down               Stop and remove containers, networks, images, and volumes
      events             Receive real time events from containers
      help               Get help on a command
      kill               Kill containers
      logs               View output from containers
      pause              Pause services
      port               Print the public port for a port binding
      ps                 List containers
      pull               Pulls service images
      restart            Restart services
      rm                 Remove stopped containers
      run                Run a one-off command
      scale              Set number of containers for a service
      start              Start services
      stop               Stop services
      unpause            Unpause services
      up                 Create and start containers
      version            Show the Docker-Compose version information

##完整的shakeshake的例子
    docker-compose.yml

    version: '2'
    services:
      shakeapp:
        image: wen/tomcat:v8
        ports:
          - "8082:8080"
        volumes:
          - /var/lib/docker/workspace/shakeshake/webapps:/opt/app/tomcat/webapps
        networks:
          shake1:
            aliases:
              - shakeapp1
      redis:
        image: wen/redis:v3.0
        networks:
          - shake1
    networks:
      shake1:
