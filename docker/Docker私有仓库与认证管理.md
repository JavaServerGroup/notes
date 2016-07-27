#docker registry server
docker registry 分为2个版本，第一版是python写成的，第二版是go写的。
本文档基于go版本，需要docker version 1.6以上。

#running on localhost
    docker run -d -p 5000:5000 --restart=always --name registry registry:2
    docker pull ubuntu && docker tag ubuntu localhost:5000/ubuntu
    docker push localhost:5000/ubuntu
    docker pull localhost:5000/ubuntu
    docker stop registry && docker rm -v registry

#本地存储
默认的registry data是存储在docker volume中的，如果registry停止volume是会被删除的(见docker volume)，可以配置为docker data volume。

    docker run -d -p 5000:5000 --restart=always --name registry -v `pwd`/data:/var/lib/registry registry:2

还有一种做法是在configuring options中指定自己的storage backend

# running a domain registry using TLS
## 创建证书
    $openssl genrsa -out server.key 2048
    $openssl req -new -key server.key -out server.csr
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [XX]:CN
    State or Province Name (full name) []:guangdong
    Locality Name (eg, city) [Default City]:shenzheng
    Organization Name (eg, company) [Default Company Ltd]:afmobi
    Organizational Unit Name (eg, section) []:soft
    Common Name (eg, your name or your server's hostname) []:myregistrydomain.com
    Email Address []:zhanbo.wen@afmobigroup.com
    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
    $openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

然后把证书安装到系统中

    cp domain.crt /etc/pki/ca-trust/source/anchors/
    update-ca-trust enable
    update-ca-trust extract

然后在/etc/hosts中配置域名，重启docker进程

    service docker restart
    docker run -d -p 5000:5000 --name registry -v $PWD/certs:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key registry:2

在其他服务器中也进行安装证书和配置域名并重启docker进程后就可以使用了，
    
    docker pull ubuntu && docker tag ubuntu myregistrydomain.com:5000/ubuntu
    docker push myregistrydomain.com:5000/ubuntu
    docker pull myregistrydomain.com:5000/ubuntu
    docker stop registry && docker rm -v registry

##用户名密码应用
    yum install httpd-tools
    htpawwd -c htpasswd testUser

    docker run -d -p 5000:5000 --restart=always --name registry \
      -v `pwd`/auth:/auth \
      -e "REGISTRY_AUTH=htpasswd" \
      -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
      -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
      -v `pwd`/certs:/certs \
      -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
      -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
      registry:2

    docker login myregistrydomain.com:5000

#compose file

    registry:
      restart: always
      image: registry:2
      ports:
        - 5000:5000
      environment:
        REGISTRY_HTTP_TLS_CERTIFICATE: /certs/domain.crt
        REGISTRY_HTTP_TLS_KEY: /certs/domain.key
        REGISTRY_AUTH: htpasswd
        REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
        REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
      volumes:
        - /path/data:/var/lib/registry
        - /path/certs:/certs
        - /path/auth:/auth
