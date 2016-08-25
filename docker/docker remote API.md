
#remote API
docker remote API 提供了一种远程管理docker系统的方法。
因为docker进程默认采用宿主机套接字上，所以需要修改docker启动配置文件
以centos7为例
    
    vi /usr/lib/systemd/system/docker.service
    修改ExecStart字段，后面加上 -H tcp://0.0.0.0:2375
    保存退出后重新加载和启动docker
    systemctl daemon-reload
    service docker restart

然后就可以通过http访问

    curl http://172.25.10.12:2375/info
    curl http://172.25.10.12:2375/info | python -mjson.tool //json 格式化数据

详细请看[https://docs.docker.com/engine/reference/api/docker_remote_api_v1.24/](https://docs.docker.com/engine/reference/api/docker_remote_api_v1.24/)

除此之外docker公司还封装了一套python版本的API[https://docker-py.readthedocs.io/en/latest/api/](https://docker-py.readthedocs.io/en/latest/api/)
##使用https连接
docker remote api还可以配置使用https安全连接
[https://docs.docker.com/engine/security/https/](https://docs.docker.com/engine/security/https/)
