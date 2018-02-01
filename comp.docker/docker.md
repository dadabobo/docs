## Docker

Online docs
* [docker docs](https://docs.docker.com/)
* [docker reference](https://docs.docker.com/reference/)
* [Docker Hub Official Repositories](https://hub.docker.com/explore/)
* [docker-library](https://github.com/docker-library)
* [docker从入门到实践](https://github.com/yeasy/docker_practice)
* [阿里云Docker镜像库加速](http://www.cnblogs.com/chen110xi/p/6230940.html)
* [Docker 镜像加速器](https://yq.aliyun.com/articles/29941)
* [阿里云](https://www.aliyun.com/)
* [阿里云控制台](https://home.console.aliyun.com/)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)
* [Docker daemon.json](https://phpor.net/blog/post/4039)

内容
* [Docker daemon](#docker-daemon)
* [Maven创建docker镜像](#docker-maven)
* [Docker命令](#docker-command)
* [Docker组件](#docker-component)
* [Docker安装](#docker-install)
* [Docker概述](#docker-intro)

---

<span id="docker-daemon"></span>
## Docker daemon

#### Docker daemon配置

直接命令行:   
`dockerd -D --tls=true --tlscert=/var/docker/server.pem --tlskey=/var/docker/serverkey.pem -H tcp://192.168.59.3:2376`

* `docker.service` 配置  
  `/lib/systemd/system/docker.service` 连接到 `/etc/systemd/system/multi-user.target.wants/docker.service`
* `daemon.json`  
  `/etc/docker/daemon.json`。`daemon.json`不支持 `HTTP_PROXY`

**hosts 配置**  
`/etc/docker/daemon.json`的`hosts` 配置 与 `docker.service` 的 `ExecStart=/usr/bin/dockerd -H fd://` 配置的 `-H` 冲突。  
使用 `DOCKER_OPTS`： `export DOCKER_OPTS="-H tcp://0.0.0.0:2375"`

修改 `docker.service` 文件  
`ExecStart=/usr/bin/dockerd -H fd://`  
修改为  
`ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock`  

docker 客户端设置
`$DOCKER_HOST="tcp://[docker-daemon-host-ip]:2375`
可连接 docker-daemon 主机。

>Ubuntu 14.04 使用 `upstart`, 配置文件 `/etc/default/docker`   
>CentOS 6.x and RHEL 6.x, 配置文件 `/etc/sysconfig/docker`

**registry加速配置**  
使用`systemd`的系统，修改 `docker.service` (`/lib/systemd/system/`)  
`ExecStart=/usr/bin/dockerd --registry-mirror=https://4ue5z1dy.mirror.aliyuncs.com`    

或修改 `daemon.json` (`/etc/docker/`)  [推荐方式]  
`"registry-mirrors": ["https://4ue5z1dy.mirror.aliyuncs.com/"]`


#### Docker daemon 推荐配置方式  `daemon.json`
配置文件： `/etc/docker/daemon.json`  

>适用 Ubuntu 16.04及以上，CentOS 7.x and RHEL 7.x  
>`daemon.json`不支持 `HTTP_PROXY`

daemon.json全配置项 (Linux)
```json
{
  "api-cors-header": "",
  "authorization-plugins": [],
  "bip": "",
  "bridge": "",
  "cgroup-parent": "",
  "cluster-store": "",
  "cluster-store-opts": {},
  "cluster-advertise": "",
  "debug": true,
  "default-gateway": "",
  "default-gateway-v6": "",
  "default-runtime": "runc",
  "default-ulimits": {},
  "disable-legacy-registry": false,
  "dns": [],
  "dns-opts": [],
  "dns-search": [],
  "exec-opts": [],
  "exec-root": "",
  "fixed-cidr": "",
  "fixed-cidr-v6": "",
  "graph": "",
  "group": "",
  "hosts": [],
  "icc": false,
  "insecure-registries": [],
  "ip": "0.0.0.0",
  "iptables": false,
  "ipv6": false,
  "ip-forward": false,
  "ip-masq": false,
  "labels": [],
  "live-restore": true,
  "log-driver": "",
  "log-level": "",
  "log-opts": {},
  "max-concurrent-downloads": 3,
  "max-concurrent-uploads": 5,
  "mtu": 0,
  "oom-score-adjust": -500,
  "pidfile": "",
  "raw-logs": false,
  "registry-mirrors": [],
  "runtimes": {
    "runc": {
      "path": "runc"
    },
    "custom": {
      "path": "/usr/local/bin/my-runc-replacement",
      "runtimeArgs": [
        "--debug"
        ]
    }
  },
  "selinux-enabled": false,
  "storage-driver": "",
  "storage-opts": [],
  "swarm-default-advertise-addr": "",
  "tls": true,
  "tlscacert": "",
  "tlscert": "",
  "tlskey": "",
  "tlsverify": true,
  "userland-proxy": false,
  "userns-remap": ""
}
```

daemon.json全配置项 (Windows)
```json
{
  "authorization-plugins": [],
  "bridge": "",
  "cluster-advertise": "",
  "cluster-store": "",
  "debug": true,
  "default-ulimits": {},
  "disable-legacy-registry": false,
  "dns": [],
  "dns-opts": [],
  "dns-search": [],
  "exec-opts": [],
  "fixed-cidr": "",
  "graph": "",
  "group": "",
  "hosts": [],
  "insecure-registries": [],
  "labels": [],
  "live-restore": true,
  "log-driver": "",
  "log-level": "",
  "mtu": 0,
  "pidfile": "",
  "raw-logs": false,
  "registry-mirrors": [],
  "storage-driver": "",
  "storage-opts": [],
  "swarm-default-advertise-addr": "",
  "tlscacert": "",
  "tlscert": "",
  "tlskey": "",
  "tlsverify": true
}
```




<span id="docker-maven"></span>
## maven创建docker镜像

在线参考
* [Spring Boot with Docker](https://spring.io/guides/gs/spring-boot-docker/)
* [docker-maven-plugin](https://github.com/spotify/docker-maven-plugin)

`docker-maven-plugin`是一个docker的maven插件，用来执行docker镜像的制作和上传。

有两种方式
1. 使用Dockerfile
2. 不使用Dockerfile，直接在pom中定义

需要先修改maven的setting文件，如果要上传镜像到私服，就必须要修改此文件。
`setting.xml`
```xml
<servers>   
  <server>
    <id>harbor</id>
    <username>admin</username>
    <password>Harbor12345</password>
    <configuration>
      <email>*******</email>
    </configuration>
  </server>
</servers>
```
在pom文件中增加两个属性
```xml
<properties>
  <docker.registry>10.10.20.202/library</docker.registry>
  <tag>v7</tag>
</properties>
```

**第一种：使用Dockerfile**

Dockerfile
```
FROM 10.10.20.202/library/tomcat8:v1
ADD target/portal-0.0.1-SNAPSHOT /root/apache-tomcat-8.0.18/webapps/portal
ENTRYPOINT /root/apache-tomcat-8.0.18/bin/catalina.sh run
WORKDIR /root/apache-tomcat-8.0.18/webapps
```
在pom文件中，增加插件
```xml
<plugin>
  <groupId>com.spotify</groupId>
  <artifactId>docker-maven-plugin</artifactId>
  <version>0.4.11</version>
  <executions>
    <execution>
      <id>build-image</id>
      <phase>package</phase>
      <goals>
        <goal>build</goal>
      </goals>
      <configuration>
        <dockerDirectory>${project.basedir}</dockerDirectory>
        <imageName>${docker.registry}/${project.artifactId}:${tag}</imageName>
      </configuration>
    </execution>
    <execution>
      <id>push-image</id>
      <phase>deploy</phase>
      <goals>
        <goal>push</goal>
      </goals>
      <configuration>
        <serverId>harbor</serverId>
        <imageName>${docker.registry}/${project.artifactId}:${tag}</imageName>
      </configuration>
    </execution>
  </executions>
</plugin>
```
上述插件将创建镜像、打标签与package绑定，将上传镜像与deploy绑定，其中dockerDirectory定义了Dockerfile所在的路径，当使用dockerDirectory这个标签时，诸如baseImage、entryPoint等标签就不在生效了。

**第二种：不使用Dockerfile**

在pom文件中增加插件
```xml
<plugin>
  <groupId>com.spotify</groupId>
  <artifactId>docker-maven-plugin</artifactId>
  <version>0.4.11</version>
  <executions>
    <execution>
      <id>build-image</id>
      <phase>package</phase>
      <goals>
        <goal>build</goal>
      </goals>
      <configuration>
        <baseImage>10.10.20.202/library/tomcat8:v1</baseImage>
        <resources>
          <resource>
            <targetPath>/root/apache-tomcat-8.0.18/webapps/emp-portal</targetPath>
            <directory>${project.build.directory}/${project.build.finalName}</directory>
          </resource>
        </resources>
        <imageName>${docker.registry}/${project.artifactId}:${tag}</imageName>
        <entryPoint>["/root/apache-tomcat-8.0.18/bin/catalina.sh", "run"]</entryPoint>
      </configuration>
    </execution>
    <execution>
      <id>push-image</id>
      <phase>deploy</phase>
      <goals>
        <goal>push</goal>
      </goals>
      <configuration>
        <serverId>harbor</serverId>
        <imageName>${docker.registry}/${project.artifactId}:${tag}</imageName>
      </configuration>
    </execution>
  </executions>
</plugin>
```
与第一种方式不同的地方，在于创建镜像的定义中，增加了一些标签
* baseImage：生命基础镜像，相当于Dockerfile中的FROM
* recerouse：说明将哪个目录拷贝到镜像的哪个路径下
* entrypoint：说明镜像启动后执行什么指令



---
<span id="docker-command"></span>
## Docker命令

Docker命名大致分为几类：
* 容器生命周期管理: `docker` [`run`|`start`|`stop`|`restart`|`kill`|`rm`|`pause`|`unpause`]
* 容器操作运维: `docker` [`ps`|`inspect`|`top`|`attach`|`events`|`logs`|`wait`|`export`|`port`|
  `exec`|`create`|`rename`]
* 容器rootfs命令: `docker` [`commit`|`cp`|`diff`]
* 镜像仓库: `docker` [`login`|`logout`|`pull`|`push`|`search`]
* 本地镜像管理: `docker` [`images`|`rmi`|`tag`|`build`|`history`|`save`|`load`|`import`]
* 其他命令: `docker` [`info`|`version`]

```
network   Manage Docker networks
node      Manage Docker Swarm nodes
service   Manage Docker services
stats     Display a live stream of container(s) resource usage statistics
swarm     Manage Docker Swarm
update    Update configuration of one or more containers
volume    Manage Docker volumes
```

#### 常用命令示例

运行镜像
`docker run -it --rm ubuntu bash`

进入一个容器
`docker exec -it webserver bash`

清理所有处于终止状态的容器  
`docker rm $(docker ps -a -q)`

清除虚悬镜像  
`docker rmi $(docker images -q -f dangling=true)`

#### nginx
`docker run --name webserver -d -p 80:80 nginx`  
`docker exec -it webserver bash`  

Dockerfile
```
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```
`docker build -t nginx:v3 .`  
`docker run --name web2 -d -p 81:80 nginx:v3`  

`docker build -f Dockerfile -t nginx:v3 .`  


#### Docker镜像
1. 获取镜像：`docker pull [OPTIONS] NAME[:TAG]`  
    `docker pull ubuntu:16.04`   
    `docker pull registry.domain.com:5000/mongo:latest`
2. 列出本地镜像: `docker images`  
3. 修改容器创建镜像：`docker commit <container> [repo:tag]`  
    `docker commit -m "some tools installed" fcbd0a5348ca seanlook/ubuntu:14.10_tutorial`
4. 利用`Dockerfile`创建镜像: `docker build`
5. 从本地文件系统导入镜像: `docker import`
6. 上传镜像: `docker push [OPTIONS] NAME[:TAG]`  
    `docker push registry.tp-link.net:5000/mongo:2014-10-27`
7. 存出镜像: `docker save`
8. 载入镜像: `docker load --input ubuntu_14.04.tar`
9. 移除本地镜像: `docker rmi`
10. 搜索镜像: `docker search TERM`   
    `docker search redis`

**获取镜像**  
`docker pull [选项] [Docker Registry地址]<仓库名>:<标签>`
* Docker Registry地址：地址的格式一般是 `<域名/IP>[:端口号]`。默认地址是 Docker Hub。
* 仓库名：如之前所说，这里的仓库名是两段式名称，既 <用户名>/<软件名>。
    对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。


#### 容器
1. 启动容器: `docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`   
    `docker run ubuntu echo "hello world"`  
    映射host到container的端口和目录  
    `-p <host_port:contain_port>`  
    `-v <host_path:container_path>`  
2. 终止容器：`docker stop`
3. 进入容器: `docker attach`
4. 导出和导入容器: `docker export`，`docker import`
5. 删除容器： `docker rm`

**运行**  
`docker run -it --rm ubuntu:14.04 bash`  
*

#### 仓库
1. `docker login`, `docker logout`
2. `docker pull`, `docker push`
3. `docker search`


---
<span id="docker-component"></span>

## Docekr 组件
* **Docker Engine**  
    Docker Engine属于Docker的运行层。这是一套轻量化运行时及工具组合，负责管理容器、镜像、构建 等等。它以原生方式运行在Linux系统之上，并由以下元素构成：  
    Docker Daemon，运行在主机计算机之上。  
    Docker Client，负责与Docker Daemon通信以执行命令。    
    REST API，用于同Docker Daemon远程交互。
* **Docker Client**  
    Docker Client是Docker架构中用户用来和Docker Daemon建立通信的客户端。用户使用的可执行文件为docker，通过docker命令行工具可以发起众多管理container的请求。
* **Docker Daemon**  
    Docker Daemon是Docker架构中一个常驻在后台的系统进程，功能是：接受并处理Docker Client发送的请求。该守护进程在后台启动了一个Server，Server负责接受Docker Client发送的请求；接受请求后，Server通过路由与分发调度，找到相应的Handler来执行请求。   
    Docker Daemon直接将执行命令发送至Docker Client——例如构建、运行以及分发等等。  
    Docker Daemon运行在主机设备之上，但作为用户，我们永远不会直接与该Daemon进行通信。  
    Docker Client也可以运行在主机设备上，但并非必需。它亦能够运行在另一台设备上，并与运行在目标主机上的Docker Daemon进行远程通信。  
    Docker Daemon启动所使用的可执行文件也为docker，与Docker Client启动所使用的可执行文件docker相同。在docker命令执行时，通过传入的参数来判别Docker Daemon与Docker Client。  
* **Dockerfile**
    Dockerfile是我们编写指令以构建Docker镜像的载体。这些指令包括：  
    `RUN apt-get y install some-package`:安装某软件包  
    `EXPOSE 8000`: 开放端口  
    `ENV ANT_HOME /usr/local/apache-ant`：传递环境变量  
    类似的指令还有很多。一旦设置了`Dockerfile`，大家就可以利用`docker build`命令来构建镜像了。
* **Docker镜像**  
    镜像属于只读模板，大家可以借此配合Dockerfile中的编写指令集进行容器构建。镜像定义了打包的应用程序以及其相关依赖。这些依赖就好像是其启动时需要运行的进程。  
    Docker镜像利用Dockerfile实现构建。Dockerfile中的每条指令都会在镜像中添加一个新的“层”，这些层则表现为镜像文件系统中的一个分区——我们可以对其进行添加或者替换。层概念正是Docker轻量化的基础。Docker利用一套Union File System建立起这套强大的结构  
* **Union File System**  
    Docker利用Union File System以构建镜像。大家可以将Union File System视为一套可堆叠文件系统，这意味着各文件系统中的文件与目录（在Docker中被称为分支）可以透明方式覆盖并构成单一文件系统。
* **Volumes**  
    Volumes属于容器中的“数据”部分，会在容器创建时进行初始化。Volumes机制允许我们持久保留并共享容器数据。数据卷 独立于默认Union File System之外，且作为普通目录及文件存在于主机文件系统当中。因此即使是在对容器进行销毁、更新或者重构时，数据卷仍然不受影响。当我们需要对某一数据卷进行更新时，直接加以变更即可。
* **Docker容器**  
    容器是从镜像创建的运行实例。
    Docker容器将一款应用程序的软件打包在单一环境当中，同时包含全部运行必需的要素。其中包括操作系统、应用程序代码、运行时、系统工具、系统库等等。Docker容器由Docker镜像构建而成。由于镜像存在只读属性，因此Docker会在镜像在只读文件系统之上添加一套读取-写入文件系统以实现容器创建。
* **Docker Compose**  
    Compose is a tool for defining and running multi-container Docker applications.
* **Docker Machine**  
    Docker Machine is a tool that lets you install Docker Engine on virtual hosts, and manage the hosts with docker-machine commands.
    Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker.
* **Docker Registry**  
    The Registry is a stateless, highly scalable server side application that stores and lets you distribute Docker images.  
    Docker Registry是一个存储容器镜像的仓库。而容器镜像是在容器被创建时，被加载用来初始化容器的文件架构与目录。
* **Docker Swarm**  
    Docker Swarm是一个用于创建Docker主机（运行Docker守护进程的服务器）集群的工具，使用Swarm操作集群，会使用户感觉就像是在一台主机上进行操作。




<span id="docker-install"></span>

## Docker 安装

#### Get Docker for Ubuntu
**Install using the repository** | [online docs](https://docs.docker.com/engine/installation/linux/ubuntu/)
1. Set up the repository  
    `sudo apt-get install apt-transport-https ca-certificates curl software-properties-common`  
    `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`  
    `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable"`  
    `sudo apt-get update`  
4. Install Docker CE  
    `sudo apt-get install docker-ce`
5. Verify Docker CE installation  
    `sudo docker run hello-world`

#### Post-installation steps for Linux
Manage Docker as a non-root user
1. `sudo groupadd docker`
2. `sudo usermod -aG docker $USER`
3. Log out and log back in
4. `docker run hello-world`

Configure Docker to start on boot  
`sudo systemctl enable docker`


#### Docker官方自动安装脚本(Ubuntu,Debian适用)
`curl -sSL https://get.docker.com/ | sh`

#### 阿里云安装脚本
`curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -`  
`sudo usermod -aG docker $USER`

#### Ansible 安装    
`ansible-galaxy install --roles-path . angstwad.docker_ubuntu`

#### Docker 镜像加速器

加速器地址  
`https://4ue5z1dy.mirror.aliyuncs.com/`  

**建议配置**  
使用`systemd`的系统，修改 `/etc/docker/daemon.json`  
`{ "registry-mirrors": ["<your accelerate address>"] }`   
`{ "registry-mirrors": ["https://4ue5z1dy.mirror.aliyuncs.com/"] }`  

使用`systemd`的系统，修改 `/etc/systemd/system/multi-user.target.wants/docker.service`  
`ExecStart=/usr/bin/dockerd --registry-mirror=https://4ue5z1dy.mirror.aliyuncs.com`  
重新加载配置并且重新启动:  
`sudo systemctl daemon-reload`  
`sudo systemctl restart docker`

使用`systemd`的系统，修改 `/etc/docker/daemon.json`  
`{ "registry-mirrors": ["<your accelerate address>"] }`   
`{ "registry-mirrors": ["https://4ue5z1dy.mirror.aliyuncs.com/ubuntu"] }`  

使用`upstart`的系统, 修改 `/etc/default/docker`  
`DOCKER_OPTS="--registry-mirror=https://4ue5z1dy.mirror.aliyuncs.com"`

#### 安装 Docker Compose
[Docker Compose on github](https://github.com/docker/compose)  

`sudo curl -L "https://github.com/docker/compose/releases/download/1.11.2/docker-compose-Linux-x86_64" -o /usr/local/bin/docker-compose`  
`chmod +x /usr/local/bin/docker-compose`  


#### 安装 Docker Machine
[Docker Machine on github](https://github.com/docker/machine)

`curl -L https://github.com/docker/machine/releases/download/v0.10.0/docker-machine-Linux-x86_64 >/tmp/docker-machine`   
`chmod +x /tmp/docker-machine`  
`sudo cp /tmp/docker-machine /usr/local/bin/docker-machine`




<span id="docker-intro"></span>
## 概述

#### 基本概念
Docker包括三个基本概念：镜像、容器和仓库。理解了这三个概念，就理解了 Docker 的整个生命周期。
* 镜像 (Image)  
    Docker镜像就是一个只读的模板。镜像可以用来创建Docker容器。
    Docker提供了一个很简单的机制来创建镜像或者更新现有的镜像，用户甚至可以直接从其他人那里下载一个已经做好的镜像来直接使用。
    Docker镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。
    Docker采用分层存储的技术。
* 容器 (Container)  
    Docker利用容器来运行应用。
    容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。
    可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。
    镜像是只读的，容器在启动的时候创建一层可写层作为最上层。
* 仓库 (Repository)   
    仓库是集中存放镜像文件的场所。
    有时候会把仓库和仓库注册服务器（Registry）混为一谈，并不严格区分。实际上，仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的 标签（tag）。
    仓库分为公开仓库（Public）和私有仓库（Private）两种形式。最大的公开仓库是 Docker Hub，存放了数量庞大的镜像供用户下载。
    用户也可以在本地网络内创建一个私有仓仓库集中存放镜像文件。
    当用户创建了自己的镜像之后就可以使用 push 命令将它上传到公有或者私有仓库，这样下次在另外一台机器上使用这个镜像时候，只需要从仓库上 pull 下来就可以了。


#### 使用Docker容器应该避免的10个事情
当你最后投入容器的怀抱，发现它能解决很多问题，而且还具有众多的优点:
* 第一：它是不可变的 – 操作系统，库版本，配置，文件夹和应用都是一样的。您可以使用通过相同QA测试的镜像，使产品具有相同的表现。
* 第二：它是轻量级的 – 容器的内存占用非常小。不需要几百几千 MB，它只要对主进程分配内存再加上几十 MB。
* 第三：它很快速 – 启动一个容器与启动一个单进程一样快。不需要几分钟，您可以在几秒钟内启动一个全新的容器。

但是，许多用户依然像对待典型的虚拟机那样对待容器。但是他们都忘记了除了与虚拟机相似的部分，容器还有一个很大的优点：它是一次性的。

**容器的准则：“容器是临时的”**

这个特性“本身”促使用户改变他们关于使用和管理容器的习惯；我将会向您解释在容器中不应该做这些事，以确保最大地发挥容器的作用。
1. **不要在容器中存储数据** – 容器可能被停止，销毁，或替换。一个运行在容器中的程序版本 1.0，应该很容易被 1.1 的版本替换且不影响或损失数据。有鉴于此，如果你需要存储数据，请存在卷中，并且注意如果两个容器在同一个卷上写数据会导致崩溃。确保你的应用被设计成在共享数据存储上写入。
2. **不要将你的应用发布两份** – 一些人将容器视为虚拟机。他们中的大多数倾向于认为他们应该在现有的运行容器里发布自己的应用。在开发阶段这样是对的，此时你需要不断地部署与调试；但对于质量保证与生产中的一个连续部署的管道，你的应用本该成为镜像的一部分。记住：容器应该保持不变。
3. **不要创建超大镜像** – 一个超大镜像只会难以分发。确保你仅有运行你应用/进程的必需的文件和库。不要安装不必要的包或在创建中运行更新（yum 更新）。
4. **不要使用单层镜像** – 要对分层文件系统有更合理的使用，始终为你的操作系统创建你自己的基础镜像层，另外一层为安全和用户定义，一层为库的安装，一层为配置，最后一层为应用。这将易于重建和管理一个镜像，也易于分发。
5. **不要为运行中的容器创建镜像** – 换言之，不要使用“docker commit”命令来创建镜像。这种创建镜像的方法是不可重现的也不能版本化，应该彻底避免。始终使用 Dockerfile 或任何其他的可完全重现的 S2I（源至镜像）方法。
6. **不要只使用“最新”标签** – 最新标签就像 Maven 用户的“快照”。标签是被鼓励使用的，尤其是当你有一个分层的文件系统。你总不希望当你 2 个月之后创建镜像时，惊讶地发现你的应用无法运行，因为最顶的分层被非向后兼容的新版本替换，或者创建缓存中有一个错误的“最新”版本。在生产中部署容器时应避免使用最新。
7. **不要在单一容器中运行超过一个进程** – 容器能完美地运行单个进程（http 守护进程，应用服务器，数据库），但是如果你不止有一个进程，管理、获取日志、独立更新都会遇到麻烦。
8. **不要在镜像中存储凭据**。使用环境变量 –不要将镜像中的任何用户名/密码写死。使用环境变量来从容器外部获取此信息。有一个不错的例子是 postgres 镜像。
9. **使用非 root 用户运行进程** – “docker 容器默认以 root 运行。（…）随着 docker 的成熟，更多的安全默认选项变得可用。现如今，请求 root 对于其他人是危险的，可能无法在所有环境中可用。你的镜像应该使用 USER 指令来指令容器的一个非 root 用户来运行。”（来自 Docker 镜像作者指南）
10. **不要依赖 IP 地址** – 每个容器都有自己的内部 IP 地址，如果你启动并停止它地址可能会变化。如果你的应用或微服务需要与其他容器通讯，使用任何命名与（或者）环境变量来从一个容器传递合适信息到另一个。


#### Docker在开发和运维过程中的优势
随着公有云的发展成熟，对于开发者来说需要一个快速迁移和高速构建应用的环境。虽然公有云为我们提供了一个按需就绪的计算环境，但是开发环境的部署仍然不能敏捷化。这个时候需要一个能够忽略公有云底层架构的创建应用开发的方式，这就是容器虚拟化技术。
对于容器虚拟化技术，Docker就是代表。很多人会搞错，以为Docker就是容器技术，其实不是这样的。Docker只是容器技术的一种，这点需要首先搞明白。

对开发和运维(DevOps)人员来说，需要一种快速交付部署的应用模式。而Docker就是这样的一个工具。

Docker在开发和运维过程中，具有如下几个方面的优势。
* 更快速的交付和部署。使用Docker，开发人员可以使用镜像来快速构建一套标准的开发环境，而且在项目实施中，测试环境和生产环境可以实现持续集成。大量节约开发、测试、部署的时间。
* 更高效的资源利用。Docker容器不像虚拟机那样需要额外的管理程序，它依赖系统内核运行，所以在资源开销上比虚拟机那种形式要低很多。
* 更轻松的迁移和扩展。Docker容器几乎可以在跨操作系统、跨环境中运行，这样也就是实现了无缝迁移的效果。
* 更简单的更新管理。使用Dockerfile，可以代替以往的繁琐的更新，而且这些更新是可跟踪的，在开发环境中这种形式更为可靠。


#### 常见问题
* 使用Non root 用户  
  目前版本的docker由于使用Socket进行通讯，因此需要root用户权限 sudo xxx，或者将需要使用Docker client的用户加入docker用户组 `sudo gpasswd -a ${USER} docker`
* 网络相关问题  
  当你在网关背后需要通过代理连接docker的index数据库时，可以手动加上http_proxy环境变量来启动docker daemon  
  `HTTP_PROXY=http://proxy_server:port docker -d &`  
  更好的做法是修改`/etc/default/docker` (on ubuntu), 添加 `export http_proxy=proxy_server:port`
  同样，docker container 如果无法自动正确的从host环境中获得DNS的配置，则需要手动指定DNS服务器地址，这可以通过
  `docker -run --dns=xxx` 来实现，也可以修改`/etc/default/docker` 添加例如 `DOCKER_OPTS="-dns 8.8.8.8"`
* 特权模式  
  在正常情况下 在container内部你没有权限操作device设备，而当前版本中，container内部部分文件例如/etc/hosts;/etc/hostname; /etc/resolve.conf等文件是动态通过mount动态以只读的形式加载上来的，理论上说你应该找到合适的方法去保证这些自动生成并加载的文件的正确性 (例如 通过--dns 设置 resolve.conf )，但是如果由于特殊原因你需要手动修改，那么你可以通过特权模式启动 docker client ： docker run --privileged ，然后你可以卸载这些文件，自己再创建新的版本
* 过多的层级依赖关系  
  以Layer的方式实现APP和相关library的cheap reuse和fast update是Docker的关键所在，不过受目前AUFS文件系统的限制，默认Layer的层级最多只能达到127（曾经只有42），在实际使用中有多种情况可能导致你的container的层级关系快速增长到这个极限值，撇开这么多layer叠加以后AUFS的效率不谈，更多情况下是你无法再更新构建你的image了
  * 使用Dockerfile构建Image时，每条指令都会给最终的Image增加一层layer依赖关系.
  * 以修改，提交，再修改再提交的方式不停的调整，更新你的Image
  * 从仓库中下载的别人的Image已经包含众多的层级依赖关系，而你需要进一步更新以创建你自己的版本

  前两者在一定程度上还是你自己可能把控的，最后一种情况就没办法了。这个问题最终必将影响Docker的实际可用性，目前的解决方案包括：
  * 使用Dockerfile时,尽可能合并多个操作：例如使用 "&&" 或 ";"
  * 合并运行多个shell命令；将多个shell命令写成脚本，在dockerfile中添加并运行这个脚本
  * 通过Export再Import Image，丢弃所有历史信息和依赖关系，创建一个全新的image

  将来可能的解决方案包括：
  * 在Dockerfile中添加对多步操作的合并提交的支持
  * 外部的image Flat工具的支持，目标是能够保留历史信息等
  * 非AUFS的其它Storage解决方案
