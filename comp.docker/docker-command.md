## Docker Commands
###### Command Summary
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
Docker 的命令可以采用 docker-CMD 或者 docker CMD 的方式执行。两者一致。

###### 容器生命周期管理
```bash
docker run          # 创建一个新容器，并在其中运行给定命令
docker start        # 启动一个容器
docker stop         # 终止一个运行中的容器
docker restart      # 重启一个运行中的容器
docker kill         # 关闭一个运行中的容器 (包括进程和所有资源)
docker rm           # 删除给定的若干个容器
docker pause        # 暂停一个容器中的所有进程
docker unpause      # 将一个容器内所有的进程从暂停状态中恢复
```

###### 容器操作
```bash
docker ps           # 列出容器
docker inspect      # 显示一个容器的底层具体信息。
docker top          # 查看一个容器中的正在运行的进程信息
docker attach       # 依附到一个正在运行的容器中。
docker events       # 从服务端获取实时的事件
docker logs         # 获取容器的 log 信息
docker wait         # 阻塞直到一个容器终止，然后输出它的退出符
docker export       # 导出容器内容为一个 tar 包
docker port         # 查找一个 nat 到一个私有网口的公共口
docker create       # 创建一个新的容器但不启动它
docker exec         # 在运行的容器中执行命令
```

###### 容器rootfs命令
```bash
docker commit       # 从一个容器的修改中创建一个新的镜像
docker cp           # 从容器中复制文件到宿主系统中
docker diff         # 检查一个容器文件系统的修改
```

###### 镜像仓库
```bash
docker login        # 注册或登录到一个 Docker 的仓库服务器
docker logout       # 从 Docker 的仓库服务器登出
docker push         # 将一个镜像或者仓库推送到一个 Docker 的注册服务器
docker pull         # 从一个Docker的仓库服务器下拉一个镜像或仓库
docker search       # 在 Docker index 中搜索一个镜像
```

###### 本地镜像管理
docker` [`images`|`rmi`|`tag`|`build`|`history`|`save`|`load`|`import`]
```bash
docker images       # 列出存在的镜像
docker rmi          # 删除给定的若干个镜像
docker tag          # 为一个镜像打标签
docker build        # 从一个 Dockerfile 创建一个镜像
docker history      # 显示一个镜像的历史
docker save         # 保存一个镜像为 tar 包文件
docker load         # 从一个 tar 包中加载一个镜像
docker import       # 导入一个文件（典型为 tar 包）路径或目录来创建一个镜像
```

###### info version
```bash
docker info         # 显示一些相关的系统信息
docker version      # 输出 Docker 的版本信息
```

###### Tips
```bash
# 列出机器上的镜像
docker images  

# 搜索镜像: docker search TERM  
docker search redis

# 从registry中下拉镜像: docker pull [OPTIONS] NAME[:TAG] 
docker pull ubuntu:16.04
docker pull registry.domain.com:5000/mongo:latest

# 推送一个镜像到registry: docker push [OPTIONS] NAME[:TAG]  
docker push registry.tp-link.net:5000/mongo:2014-10-27

# 将一个container固化为一个新的镜像： docker commit <container> [repo:tag]
docker commit -m "some tools installed" fcbd0a5348ca seanlook/ubuntu:14.10_tutorial

# 启动一个container: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]  
docker run ubuntu echo "hello world"

# 映射host到container的端口和目录
#   -p <host_port:contain_port>
#   -v <host_path:container_path>

# 连接到容器执行命令
docker exec -ti mycontainer /bin/bash

# 删除所有已停止容器  
docker rm $(docker ps -a -q)

# 在容器之间共享数据: 使用 `--volumes-from` 选项

# 向/从容器拷贝数据
docker cp host.txt mycontainer:/root/host.txt
docker cp mycontainer:/root/file.txt .  
```

---
###### Command Details
```
Usage: docker [OPTIONS] COMMAND [arg...]
       docker [ --help | -v | --version ]

A self-sufficient runtime for containers.

Options:

  --config=~/.docker              Location of client config files
  -D, --debug                     Enable debug mode
  -H, --host=[]                   Daemon socket(s) to connect to
  -h, --help                      Print usage
  -l, --log-level=info            Set the logging level
  --tls                           Use TLS; implied by --tlsverify
  --tlscacert=~/.docker/ca.pem    Trust certs signed only by this CA
  --tlscert=~/.docker/cert.pem    Path to TLS certificate file
  --tlskey=~/.docker/key.pem      Path to TLS key file
  --tlsverify                     Use TLS and verify the remote
  -v, --version                   Print version information and quit

Commands:
    attach    Attach to a running container
    build     Build an image from a Dockerfile
    commit    Create a new image from a container's changes
    cp        Copy files/folders between a container and the local filesystem
    create    Create a new container
    diff      Inspect changes on a container's filesystem
    events    Get real time events from the server
    exec      Run a command in a running container
    export    Export a container's filesystem as a tar archive
    history   Show the history of an image
    images    List images
    import    Import the contents from a tarball to create a filesystem image
    info      Display system-wide information
    inspect   Return low-level information on a container, image or task
    kill      Kill one or more running containers
    load      Load an image from a tar archive or STDIN
    login     Log in to a Docker registry.
    logout    Log out from a Docker registry.
    logs      Fetch the logs of a container
    network   Manage Docker networks
    node      Manage Docker Swarm nodes
    pause     Pause all processes within one or more containers
    port      List port mappings or a specific mapping for the container
    ps        List containers
    pull      Pull an image or a repository from a registry
    push      Push an image or a repository to a registry
    rename    Rename a container
    restart   Restart a container
    rm        Remove one or more containers
    rmi       Remove one or more images
    run       Run a command in a new container
    save      Save one or more images to a tar archive (streamed to STDOUT by default)
    search    Search the Docker Hub for images
    service   Manage Docker services
    start     Start one or more stopped containers
    stats     Display a live stream of container(s) resource usage statistics
    stop      Stop one or more running containers
    swarm     Manage Docker Swarm
    tag       Tag an image into a repository
    top       Display the running processes of a container
    unpause   Unpause all processes within one or more containers
    update    Update configuration of one or more containers
    version   Show the Docker version information
    volume    Manage Docker volumes
    wait      Block until a container stops, then print its exit code

Run 'docker COMMAND --help' for more information on a command.
```
