## Docker Install and Config

###### Docker Install
Ubuntu 阿里云镜像安装  
`curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -`

ansible 安装  
`ansible-galaxy install --roles-path . angstwad.docker_ubuntu`

###### Docker “registry-mirrors”
我的加速器地址： `https://4ue5z1dy.mirror.aliyuncs.com/ubuntu`  

配置加速器
```bash
echo "DOCKER_OPTS=\"--registry-mirror=https://4ue5z1dy.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
sudo service docker restart
```

建议直接通过daemon config进行配置。(`/etc/docker/daemon.json`)  
`{ "registry-mirrors": ["<your accelerate address>"] }`
示例
`{ "registry-mirrors": ["https://4ue5z1dy.mirror.aliyuncs.com"] }`

---
###### Windows 10 Docker
从windows 10 bash 连接到 Window 10 docker。

windows 10 bash 安装 docker 最新版
```
$ wget https://get.docker.com/builds/Linux/x86_64/docker-latest.tgz
$ tar xzvf docker-latest.tgz
$ mv docker/* /usr/local/bin
```

设置 `DOCKER_HOST` (`~/.bashrc`)  
`$ export DOCKER_HOST=tcp://127.0.0.1:2375`


---
##### Docker Daemon
[Docker Daemon连接方式详解](https://www.jianshu.com/p/7ba1a93e6de4) 
[关于Ubuntu 16.04.2 LTS 下DOCKER_OPTS不生效的问题解决](http://blog.csdn.net/l6807718/article/details/51325431)

@import "img/docker/docker-client-server.png" {width=600}

Docker client/Server 模式包括:
* Docker client 命令行工具 `docker`
* Docker Daemon Server `dockerd` （早期 `docker -d`）
* Docker Registry

通常情况 Docker client 与 Docker server 通讯默认采用UNIX域套接字, 会生成一个 /var/run/docker.sock 文件, UNIX 域套接字用于本地进程之间的通讯, 这种方式相比于网络套接字效率更高, 但局限性就是只能被本地的客户端访问。

服务端开启端口监听 `dockerd -H IP:PORT` , 客户端通过指定IP和端口访问服务端 `docker -H IP:PORT`
可以同时监听多个socket: 
`sudo dockerd -H unix:///var/run/docker.sock -H tcp://127.0.0.1:2376 -H tcp://127.0.0.1:2377`

###### 创建TLS证书(根证书、服务端证书、客户端证书)
运行 `tlscert.sh` 创建证书。
@import "tlscert.sh"

客户端的证书保存在`client`目录下, 服务端的证书保存在`server`目录下 

###### 服务端配置
`sudo cp server/* /etc/docker` 

修改配置 `/etc/default/docker` (非 Ubuntu)
`DOCKER_OPTS="--selinux-enabled --tlsverify --tlscacert=/etc/docker/ca.pem --tlscert=/etc/docker/server-cert.pem --tlskey=/etc/docker/server-key.pem -H=unix:///var/run/docker.sock -H=0.0.0.0:2375"` 
重启docker: `sudo service docker restart`


修改配置 `/lib/systemd/system/docker.service` (Ubuntu 16.0.4)
修改前: `ExecStart=/usr/bin/dockerd -H fd://`
修改后： (`-H=0.0.0.0:2375` 监听任意IP)
`ExecStart=/usr/bin/dockerd -H fd:// --selinux-enabled --tlsverify --tlscacert=/etc/docker/ca.pem --tlscert=/etc/docker/server-cert.pem --tlskey=/etc/docker/server-key.pem -H=0.0.0.0:2375`
重载： 
`systemctl daemon-reload`
`systemctl restart docker.service`

###### 客户端
客户端加`tls`参数访问 （服务端IP： `192.168.99.23`）
`docker --tlsverify --tlscacert=client/ca.pem --tlscert=client/cert.pem --tlskey=client/key.pem -H tcp://192.168.99.23:2375 version`

Docker API方式访问 
`curl https://192.168.99.23:2375/images/json --cert client/cert.pem --key client/key.pem --cacert client/ca.pem` 

简化客户端调用参数配置，追加环境变量
`sudo cp client/* ~/.docker` 
`echo -e "export DOCKER_HOST=tcp://192.168.99.23:2375 DOCKER_TLS_VERIFY=1" >> ~/.bashrc`
`docker version`

> Windows 10 WSL 已配置连接到 192.168.99.23 Docker Daemon Server


###### Configure automated builds on Docker Hub
在Docker Hub上配置自动构建
* 参考 [k8s镜像：安装kubernetes](https://studygolang.com/articles/10521), 解决访问不了gcr.io问题。 
* [Configure automated builds on Docker Hub](https://docs.docker.com/docker-hub/builds/)

需要在 Docker Hub 与 GitHUB上均有账号。
1. GitHub 开启对 Docker Hub 的读授权
2. 将 Dockerfile 上传 GitHub
3. Docker Hub上创建Automated build
4. 取到本地并push到private Registry

登录到Docker Hub并选择 `Profile > Settings > Linked Accounts & Services`
选择 `Link GitHub` 完成 GitHub 对 Docker Hub 的授权。 

在 GitHub 上新建一个工程(`Dockerfiles`)，clone到本地。
然后在工程中新增： `k8s-dns-kube-dns-amd64/1.14.7/Dockerfile`。 
`Dockerfile`内容为：
```dockerfile
FROM gcr.io/google_containers/k8s-dns-kube-dns-amd64:1.14.7
MAINTAINER wangwg2
LABEL name="k8s-dns-kube-dns-amd64" license="GPLv2" build-date="20180129"
```

登录到Docker Hub并选择 `Profile > Create Automated Build`
选择 Github 上 `Dockerfiles` 中的 `k8s-dns-kube-dns-amd64/1.14.7/Dockerfile`.