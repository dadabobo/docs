## Docker Registry
搭建Docker私有仓库。

###### Prepare
环境说明
```yaml
work  : 192.168.99.23  # registry.me
ubuntu: 192.168.99.23  # nginx.me  
```

###### Start Registry
使用官方的 docker registry 镜像来启动本地的私有仓库。(192.168.99.23) 
```bash
# 启动registry  
docker run -d -p 5000:5000 --name registry registry:2
# pull 镜像 (Docker Hub)  
docker pull busybox
# tag 镜像
docker tag busybox localhost:5000/wangwg/firstimage
# push 镜像
docker push localhost:5000/wangwg/firstimage
# pull 镜像
docker pull localhost:5000/wangwg/firstimage
# 停止registry
docker stop registry && docker rm -v registry

## 指定数据卷
docker run -d -p 5000:5000 -v /data/docker/voldata/registry:/var/lib/registry \
  --restart=always --name registry registry:2
```

###### Insecure Registry
所有需要访问仓库的 Docker daemon 设置 `--insecure-registry` 选项
```bash
docker pull registry.me:5000/wangwg/firstimage  ## 错误   
## 修改 /etc/docker/daemon.json 增加
#   "insecure-registries":["registry.me:5000"]  
docker pull registry.me:5000/wangwg/firstimage  ## OK  
```

`/etc/docker/daemon.json`
```json
{
  "insecure-registries":["registry.me:5000"],
  "registry-mirrors": ["https://4ue5z1dy.mirror.aliyuncs.com/"]
}
```


##### Secure Registry
Docker官方是推荐你采用Secure Registry的工作模式的，即transport采用tls。这样我们就需要为Registry配置tls所需的key和crt文件了。

签发证书: CA主机, 工作目录 `opensslca`  
```bash
openssl genrsa -out certs/registry.me.key 2048
openssl req -config imCA.cnf -new -sha256 \
    -key certs/registry.me.key -out certs/registry.me.csr -subj "/CN=registry.me"
openssl ca -config imCA.cnf -extensions server_cert -days 375 -notext -md sha256 \
    -in certs/registry.me.csr -out certs/registry.me.crt
```

###### host: registry.me
运行 docker registry
```bash
docker run -d -p 443:443 --restart=always --name registry \
  -v /docker/certs:/certs \
  -v /docker/voldata/registry:/data \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \  
  -e REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/data \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.me.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/registry.me.key \
  registry:2
```
或 `docker-compose -f docker-registry.yml up -d`
```yaml
# file: docker-registry.yml
registry:
  image: registry:2
  container_name: me-registry
  ports:
    - "443:443"
  volumes:
    - /data/docker/certs:/certs
    - /data/docker/voldata/registry:/data
  restart: always
  environment:
    - REGISTRY_HTTP_ADDR=0.0.0.0:443
    - REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/data
    - REGISTRY_HTTP_TLS_KEY=/certs/registry.me.key
    - REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.me.crt
```

访问 Docker Registry
```bash
docker tag busybox:latest registry.me/wangwg/busybox:latest
docker push registry.me/wangwg/busybox
curl https://registry.me/v2/_catalog
```
在主机01/02上正常导入证书，则对应主机 `docker pull/push registry.me/xx` 可以正常使用。  
如未导入证书，对应主机报证书错：x509: certificate signed by unknown authority。  

###### htpasswd
使用 htpasswd 建立基于用户的认证。
```bash
# 创建用户 `testu/test123`  
mkdir auth
docker run --rm --entrypoint htpasswd registry:2 -Bbn testu test123 > auth/htpasswd
# 或 (ubuntu 需要安装 bcrypt)
htpasswd -Bbn testu test123 > auth/htpasswd
```

启动registry
```bash
docker run -d -p 443:443 --restart=always --name registry \
  -v /docker/config/auth:/auth \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \  
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -v /docker/voldata/registry:/data \
  -e REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/data \
  -v /docker/certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.me.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/registry.me.key \
  registry:2
```
或运行: `docker-compose -f docker-registryauth.yml up -d`
```yaml
# file: docker-registryauth.yml
registry:
  image: registry:2
  container_name: me-registryauth
  ports:
    - "443:443"
  volumes:
    - /data/docker/certs:/certs
    - /data/docker/config/auth:/auth
    - /data/docker/voldata/registry:/data
  restart: always
  environment:
    - REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/data
    - REGISTRY_HTTP_ADDR=0.0.0.0:443
    - REGISTRY_AUTH="htpasswd"
    - REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm"
    - REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd
    - REGISTRY_HTTP_TLS_KEY=/certs/registry.me.key
    - REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.me.crt
```

```bash
# docker Login (username:testu, password: test123)
docker login registry.me
docker push registry.me/wangwg/busybox  ## OK

# 通过V2版Rest API可以查询Repository和images  
curl --basic --user testu:test123 https://registry.me/v2/_catalog
```

> htpasswd工具
>`sudo apt-get install apache2-utils` 安装 `htpasswd`  
>`htpasswd -c registry.password USERNAME`  

> **tips**: nginx 与 docker registry 的 htpasswd 加密算法可能不同。
> docker registry (TLS) 只支持 bcrypt 格式的密码 (`-B` 选项)
> `htpasswd -Bbn testu test123`

###### Docker Registry + Nginx
SSL支持，authentication (htpasswd)  (testu/test123)
`htpasswd -c registry.password USERNAME`  

`docker-compose -f docker-registry-nginx.yml`
```yaml
# file: docker-registry-nginx.yml
nginx:
  image: "nginx:latest"
  ports:
    - 443:443
  links:
    - registry:registry
  volumes:
    - /docker/config/nginx/conf/:/etc/nginx/conf.d:ro
registry:
  image: registry:2
  ports:
    - 127.0.0.1:5000:5000
  environment:
    REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
  volumes:
    - /docker/voldata/registry:/data
```

配置 nginx：`nginx.conf` 
```yaml
upstream docker-registry {
  server registry:5000;
}

server {
  listen 443;
  server_name nginx.me;

  # SSL
  ssl on;
  ssl_certificate /etc/nginx/conf.d/nginx.me.crt;
  ssl_certificate_key /etc/nginx/conf.d/nginx.me.key;

  # disable any limits to avoid HTTP 413 for large image uploads
  client_max_body_size 0;

  # required to avoid HTTP 411: see Issue #1486 (https://github.com/docker/docker/issues/1486)
  chunked_transfer_encoding on;

  location /v2/ {
    # Do not allow connections from docker 1.5 and earlier
    # docker pre-1.6.0 did not properly set the user agent on ping, catch "Go *" user agents
    if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
      return 404;
    }

    # To add basic authentication to v2 use auth_basic setting plus add_header
    ## user/passwd:  wangwg/to2passwd
    auth_basic "registry.localhost";
    auth_basic_user_file /etc/nginx/conf.d/registry.password;
    add_header 'Docker-Distribution-Api-Version' 'registry/2.0' always;

    proxy_pass                          http://docker-registry;
    proxy_set_header  Host              $http_host;   # required for docker client's sake
    proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
    proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Proto $scheme;
    proxy_read_timeout                  900;
  }
}
```

###### Access Docker Registry
```bash
docker login https://nginx.me
docker tag busybox:latest nginx.me/wangwg/firstimage
docker push nginx.me/wangwg/firstimage
docker pull nginx.me/wangwg/firstimage
curl https://nginx.me/v2/_catalog
```