<span id="coreos07"></span>
## 7. Docker
CoreOS实践指南（七）：Docker容器管理服务

在“漫步云端：CoreOS实践指南”系列的前几篇文章中，ThoughtWorks的软件工程师林帆主要介绍了CoreOS及其相关组件和使用。说到CoreOS，不得不提Docker。当Docker还名不见经传的时候，CoreOS创始人Alex就凭着敏锐直觉，预见了这个项目的价值，将Docker做为了这个系统支持的第一套应用程序隔离方案。本文将主要介绍在具体的场景下，如何在CoreOS中恰当的管理Docker容器。

这次的主角终于轮到了大鲸鱼Docker。不晓得有多少人是因为Docker认识了CoreOS的，至少它在社区的知名度事实上高于CoreOS项目本身。这篇文章里不会对Docker做很深入的讲解，而重点放在开始使用Docker所需的基本知识以及在CoreOS中使用Docker托管服务的推荐实践方法。

#### 结缘
雷教主说，“站在风口上，猪也能飞起来”。Docker正是借着云计算的风飞上了天。伴随着Docker和应用容器的兴起，拉动了一批PaaS产品的发展，而CoreOS也借了这股劲儿赚足了人气，进行得风生水起。同时CoreOS的成熟也在回馈Docker社区，为社区带来了例如Etcd、Deis（私有PaaS云平台，目前是基于CoreOS构建的）等许多新的活力。

说起CoreOS与Docker的渊源，确有一段历史了。故事大致是这样开始的，2013年2月，美国的dotCloud公司发布了一款新型的Linux容器软件Docker，并建立了一个网站发布它的首个演示版本（ 见Docker第一篇官方博客 ）。而几乎同时，2013年3月，美国加州，年轻的帅小伙Alex Polvi正在自己的车库开始他的第二次创业。此前，他的首个创业公司Cloudkick卖给了云计算巨头Rackspcace（就是OpenStack的东家）。

有了第一桶金的Alex这次准备干一票大的，他计划开发一个足以颠覆传统的服务器系统的Linux发行版。为了提供能够从任意操作系统版本稳定无缝地升级到最新版系统的能力，Alex急需解决应用程序与操作系统之间的耦合问题。因此，当时还名不见经传的Docker容器引起了他的注意，凭着敏锐直觉，Alex预见了这个项目的价值，当仁不让地将Docker做为了这个系统支持的第一套应用程序隔离方案。不久以后，他们成立了以自己的系统发行版命名的组织：CoreOS。事实证明，采用Docker这个决定，后来很大程度上成就了CoreOS的生态系统。

现在看来，CoreOS已经不是唯一预装了Docker的操作系统了，但它是第一个，也是目前做得最成功的一个。RedHat和Canonical（Ubuntu的母公司）随其后也分别推出了自己的预装Docker的系统发行版，但知悉者寥寥，并没有做成气候。其项目发起时间见下图（出自 成都ThoughtWorks技术雷达分享活动 ），Atomic和Ubuntu Core Snappy分别是RedHat和Canonical公司推出的预装Docker的操作系统，目标也都是直指服务器集群和容器化部署。

#### 应用容器
“应用容器”现在对许多人已经并不陌生了。但它在服务器的系统上还不是那么普及，至少与你手上的智能手机系统相比。至今在服务器系统上流行的安装软件方式依然是编译源代码、手工的安装包或各种包管理工具，虽然包管理工具的出现解决了应用软件安装、卸载以及自身依赖等诸多问题，却无法很好的解决软件之间的依赖冲突。而早在Docker诞生以前，“沙盒”的概念已经被普遍使用在Android、iOS等主流的手机系统中了。通过沙盒的隔离，应用软件将自己所有的依赖与应用本身打包在一起，并通过SDK API提供的可控的方式访问操作系统，软件与系统的耦合度大大降低。这样带来的直接好处是，软件之间的依赖冲突得到了很好的解决，移除一个应用软件一般只需要很短的几秒钟并且彻底无痕，软件访问系统的安全性也更加可控。

事实上，Android实现沙盒同样的基于Linux内核的cgroup和namespace机制用于限制和隔离资源的使用，所使用的技术与Docker如出一辙。这些早在Linux 2.6.x版本就已经加入了的新特性，已经通过了较长时间的检验，被证实是可行并且可靠的。

#### 当CoreOS 遇见 Docker
这篇文章里不会专门介绍Docker的使用，而是关注在具体的场景下，如何在CoreOS中恰当的管理Docker容器。了解过Docker在CoreOS生态系统中的角色后，下面通过在两个容器中分别运行NodeJS和MongoDB的例子说明如何在CoreOS中通过Systemd管理服务，并在此基础上快速浏览一些基本的Docker命令。

##### 制作服务的Docker镜像
服务镜像有一些可以是现成的标准服务的镜像，例如MongoDB服务。另一些则需要经过用户定制，制作Docker镜像文件一般可以通过Dockerfile或现有容器实例生成两种方法。前者是比较推荐的做法，但需学习Dockerfile的写法，已经超出了这个系列的范围。后者相对简单，但不利于后期的镜像维护管理，这里仅仅作为演示目的，因此采用这种方法。

###### 1.拉取基础镜像  
每一个具体的容器实际上是运行在虚拟出来的独立空间里面的，它被设计成只能够访问到存在于同一个虚拟空间下面的其他文件。因此为了使应用能够使用基本的运行时依赖，还需要将一些Linux的命令和配置文件也打包放到虚拟空间里，这种打包好的依赖文件集合就是镜像。

操作 docker 的方式与 `systemctl`、`etcdctl` 类似，需要由一个二级命令共同组成一个完整的命令。通过
`docker pull`
命令可以指定的网络地址拉取镜像到本地（如果指定的是名称而不是网络地址，则会在docker官方的镜像仓库里面搜索，比如下面的两个例子）。
```
$ docker pull node:latest
...
Status: Downloaded newer image for node:latest
$ docker pull mongo:latest
...
Status: Downloaded newer image for mongo:latest
```
镜像是按照“地址/镜像名:版本标签”格式命名的，其中镜像名是必须的，如果地址部分为空则默认为官方仓库地址。如果版本标签部分为空，对于较新的Docker版本（大约1.3.x以后），会仅仅下载标签为latest的版本，而较早版本的Docker则会下载指定镜像的所有版本，常常会因此意外下载许多不需要的镜像版本。

在一大段输出以后，若一切顺利（事实是，在国内可能不会太顺利），本地的Docker已经可以直接使用这两个预装了`NodeJS`和`MongoDB`的镜像了。可以通过 `docker images` 命令验证。
```
$ docker images
REPOSITORY TAG IMAGE ID CREATED VIRTUAL SIZE
node latest 61afc26cd88e 3 days ago 696.2 MB
mongo latest 59b3d123f9b8 6 days ago 392.4 MB
...
```
在国内的一些地区，拉取官方镜像仓库的镜像可能会失败（或许是大名鼎鼎的某防火墙的功劳）。此时可以采用国内的第三方开源镜像仓库，比如 `DockerPool` 或 `Docker.cn` 提供的镜像文件。前者需要配置本地的SSL证书，否则会遇到“`Error: Invalid registry endpoint`”错误，略微麻烦。后者可以直接使用：
```
docker pull docker.cn/docker/node:latest
docker pull docker.cn/docker/mongo:latest
```

###### 2.制作定制镜像
`MongoDB`可以直接使用官方的Docker镜像。而`NodeJs`的容器还需要些许定制，将应由部署到容器中然后生成新的镜像。再次说明，制作镜像的最佳途径是写一个`Dockerfile`，实现基础设施可视化。以下通过修改现有镜像的方法一般只用于演示目的。

接下来我们要分别启动MongoDB和NodeJs的容器实例，并将MongoDB的端口暴露到NodeJs的容器中。

首先启动一个`MongoDB`容器实例，命名为 `mongo-ins` 。启动容器的命令是` docker run` ，除了运行配置参数如 `--name`、 `--port` 等，这个命令的最后两个参数分别是实例使用的镜像名字，和实例本身需要运行的命令。有的容器已经配置好了默认的运行程序，此时后面的一个参数可以省略，比如下面的例子。
参数 `-d `表示运行后直接进入后台，屏幕上回显的一串输出是新启动容器实例的ID。
然后启动一个`NodeJs`容器实例，使用官方的node镜像作为基础镜像，并将它与 `mongo-ins` 实例建立“连接”。这个容器实例命名为 `node-app` 。
```
$ docker run --name node-app -p 3000 --link mongo-ins:mongo -it node /bin/bash
root@e73e7d7836a6:/# <— 已经进入容器中的Bash>
```
-it 实际上是 -i -t 的简便写法，表示启用交互式模式和启用显示终端，这样我们可以进入容器中做一些手工操作。而参数 --link 用来将两个容器进行关联，关于Docker Link的用法可以参考 Docker的相关文档 。简单来说，Link的参数 mongo-ins:mongo 表示将容器 mongo-ins 引入到正在建立的容器镜像中，并将其称为 mongo。这样做的结果是，在新建的 node-app 容器实例中，能够访问到两个全局环境变量： <img src="https://latex.codecogs.com/gif.latex?MONGO_PORT_27017_TCP_ADDR 和"/>MONGO_PORT_27017_TCP_PORT，分别是用来访问 MongoDB 的 IP 地址和端口。
作为演示，我们将在容器中部署一个从Github获取的简单示例。
```
$ git clone
https://github.com/ijason/NodeJS-Sample-App.git
$ cd /NodeJS-Sample-App/EmployeeDB

$ sed -i -e "s/27017/process.env.MONGO_PORT_27017_TCP_PORT/" -e "s/'localhost'/process.env.MONGO_PORT_27017_TCP_ADDR/" app.js
$ exit
```
上面的第三条命令将原本容器中指定的 `MongoDB` 位置改成了从另一个容器中暴露的IP地址和端口。至此这个node-app容器已经部署好了一个名为 `Employees` 的示例应用，接下来将它生成镜像并放到集群的每个节点上。

###### 3.生成并提交镜像
为了在集群里对容器中的服务提供横向扩展能力，需要将定制好的容器在集群的所有节点共享。
首先需要一个存放共享镜像的地方，在企业环境可以使用私有的镜像仓库，但为了演示简便起见，我们直接使用Docker的公共仓库。首先需要在 `Docker Hub` 注册一个用户，然后使用 `docker login` 命令登陆到仓库服务器。
```
$ docker login
Username: linfan
Password:
Email: linfan@******.com
Login Succeeded
```
然后我们需要将本地修改过的容器使用 `docker commit` 命令生成一个本地的镜像。注意，由于之后需要将镜像提交至`Docker Hub`，这里镜像的名字必须以自己的`Docker Hub`用户名作为前缀，否则在后面的 `push` 时候会遇到 `403 “Access Denied: Not allowed to create Repo at given location”` 错误。例如名为 `linfan/employees`。
```
$ docker commit node-app linfan/employees
a4281aa8baf9aee1173509b30b26b17fd1bb2de62d4d90fa31b86779dd15109b
$ docker images
REPOSITORY TAG IMAGE ID CREATED VIRTUAL SIZE
linfan/employees latest a4281aa8baf9 14 seconds ago 696.2 MB
```
最后，使用 `docker push` 命令将这个准备好的镜像提交到`Docker Hub`仓库中。
```
$ docker push linfan/employees
The push refers to a repository [linfan/employees] (len: 1)
Sending image list
...
Pushing tag for rev [5577d6743652] on {https://cdn-registry-1.docker.io/v1/repositories/linfan/employees/tags/latest}
```
提交完成后，在其他节点就可以使用 `docker pull` 命令获取到这个镜像了。
注意：严格来说，将数据库服务容器通过`Docker Link`暴露给应用服务容器的方法并不符合分布式应用的 12条准则 ，因为通过`Docker Link`连接的两个容器必须运行在同一个物理主机上，数据与应用不能在集群中分别独立的部署或横向扩展。

##### 使用 Fleet 启动服务容器
###### 1.编写 Unit 文件
有了相应的服务容器后，在CoreOS中正确启动服务的方法应该是通过Fleet来管理。通过合理使用 `Unit` 的 `X-Fleet` 配置，能够很好的解决容器直接相互依赖的问题。

用 `vagrant ssh` 进入一个 CoreOS 的 `Shell` 中，创建以下两个服务 `Unit` 文件。

首先是`mongo.service`
```
[Unit]
Description=General MongoDB Service
After=docker.service
[Service]
TimeoutStartSec=0
ExecStart=/opt/bin/docker-run.sh --name mongo-ins -d mongo
ExecStop=/usr/bin/docker stop mongo-ins
```
然后是`employees.service`，请注意它的 `Unit` 和 `X-Fleet` 段的内容。在`Unit`段指定了这个服务启动前必须首先启动 `mongo.service` 服务，而在 `X-Fleet` 段指定了自己需要运行在与 `mongo.service` 相同的服务节点上。
```
[Unit]
Description=Employee Information Management Service
After=docker.service
After=mongo.service
[Service]
TimeoutStartSec=0
ExecStart=/opt/bin/docker-run.sh -p 3000:3000 --link mongo-ins:mongo -d --name node-app node-app node /NodeJS-Sample-App/EmployeeDB/app.js
ExecStop=/usr/bin/docker stop mongo-ins
[X-Fleet]
X-ConditionMachineOf=mongo.service
```
上面的两个 Unit 文件都使用到了一个 `/opt/bin/docker-run.sh` 脚本，用于替代 `docker run` 命令。这个脚本需要额外创建并放置到 `/opt/bin` 目录下面，其作用是检测是否已经有一个同名的容器在运行了，如果没有则执行相应的 `docker run `命令，否则直接使用 `docker start` 命令启动已经存在的容器。其内容如下：
```bash
#!/bin/bash
PARA="${*}"
NAME=$(echo "${PARA}" | grep '\-\-name' | sed 's/.*--name \([^ ]*\).*/\1/g')
if [ "${NAME}" == "" ]; then
echo "[ERROR] Must specify a name to the container!";
exit -1;
fi
EXIST=$(sudo docker ps -a | grep "${NAME}[ ]*$")
if [ "${EXIST}" == "" ]; then
sudo docker run ${PARA}
else
sudo docker start ${NAME}
fi
```

###### 2.启动服务
通过 `fleetctl` 命令启动服务，具体的用法在系列前面的内容里面已经介绍过了。
```
fleetctl start ./mongo.service
fleetctl start ./employees.service
```
这里为了简便直接用了 `fleetctl start` 命令，更推荐的启动服务方法请参考系列中关于`Fleet`的一篇。
到这一步，这个部署在容器中的服务已经可以使用了。从外部访问服务器的 `3000` 端口即可打开下面这个页面，并向`MongoDB`服务中的数据库中添加员工信息了。

##### 管理容器运行状态
最后，再来看一些用于检测容器运行状态和日常管理的Docker命令。

###### 1.查看运行日志  
容器通过 `-d` 参数进入后台运行之后，其中服务输出的日志内容可以通过 `docker logs` 命令查看到。
```
$ docker logs mongo-ins
MongoDB starting : pid=1 port=27017 dbpath=/data/db 64-bit host=d9bba1bfc8be
...
```

###### 2.容器实例列表
命令 `docker ps` 能够列出所有当前正在运行的容器的基本信息。
```
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
d9bba1bfc8be mongo:2 "/entrypoint.sh" 4 minutes ago Up 4 minutes 27017/tcp mongo-ins
22de21d77174 node:0 "/bin/bash" 3 minutes ago Up 5 minutes node-app
...
```

###### 3.容器实例详情
使用 `docker inspect` 命令能够查看到指定一个容器的详细运行信息。  
`<img src="https://latex.codecogs.com/gif.latex?docker inspect mongo-ins{ ... }`

###### 4.备份和还原容器
简单的提一下，用来将现有的本地镜像打包备份和还原的命令是 `docker save` 和 `docker load`。也可以直接将容器实例打包，相关命令是 `docker export` 和 `docker import` ，注意 `import` 之后会将备份的数据恢复成一个新的本地镜像，而不是容器实例。

这两个命令的使用可以 [参考文档](http://docs.docker.com/reference/commandline/cli/)。只额外说明一个问题，既然两种还原都会将备份的内容还原为容器，为什么需要两种还原命令呢？原因在于使用 `save` 和 `export` 生成的打包效果是不太一样的，简单说就是 `export` 生成的备份会丢弃所有的镜像分层结构，而 `save` 生成的备份不会。镜像分层结构有利于减少相似镜像本地存储所需的空间，细节可参考[这篇文章](http://code.csdn.net/news/2822693)。

以上介绍的这些命令仅仅是Docker强大功能的冰山一角，网络上已经有许多十分优秀的Docker使用教程，作为学习Docker和应用容器都是极好的途径。这里推荐一个Dockerone翻译的[Docker系列文章](http://dockone.io/article/111) 。

#### 后话
事实上，随着CoreOS的独立容器项目 Rocket 的发起，Docker 在未来将不再是 CoreOS 和其他Linux操作系统设计容器方案的唯一选择。但作为 CoreOS 乃至整个 Linux 生态圈的应用容器服务佼佼者，Docker的王者地位还会持续很长的时间，而CoreOS始终会保持对Docker容器的一流支持（见CoreOS关于 Rocket博客中的F&Q ）。

正值提笔写这篇文章的那天，Bing的首页内容是泰国的曼谷港，这幅画面与Docker的Logo颇有几分神似。如此的巧合，使人不由的联想，这艘万吨货轮底下是否也正藏着一只蓄势待发的蓝鲸呢。

在这一篇内容中，将重点放在了使用Docker容器管理服务的介绍，正如文章中已经指出的，例子中的有些实践（使用`docker commit`创建镜像，以及`fleet start`直接启动服务等）并不适合在实际的项目中使用。从下下篇的文章起，我们将讲解几个完整的，符合产品应用的例子。在进入正式的综合实例前，在下一篇中，会对 `Systemd` 和 `Fleet` 使用的 Unit 文件做一个更深入的探索。


<span id="coreos08"></span>
## 8. Unit
CoreOS实践指南（八）：Unit文件详解

在 "CoreOS实践指南" 系列前面的内容里，我们已经介绍了使用 Unit 文件配置 `Systemd` 管理的系统服务的方式，以及 CoreOS 的 `Fleet` 工具继承并扩展了这种文件格式，使得它更加适用于集群环境的服务配置。由于 `Unit` 文件本身包罗万象，且属于相对进阶的内容，在系列前面的部分的文章中，都并没有很详细的讲解 `Unit` 文件具体的格式和可用的参数。而事实上，这部分的知识正是通往 CoreOS 系统管理高手的必经之路。现在，该是继续深入的时候了。

这个部分的内容并不是十分独立，需要阅读者对`Systemd`和 `Fleet`有一定了解，如果您还没有阅读过这两部分的内容，有必要先返回复习一下。

##### Systemd的Unit文件
在 Systemd 的生态圈中（除了 CoreOS 外，目前的主流 Linux 系统，如 Arch、SUSE、Fedora、RedHat/CentOS 也都已经使用了 `Systemd`，此外 Ubuntu 也将最快于15.04版本启用 `Systemd` 作为默认的系统管理工具），`Unit` 文件统一了过去各种不同的系统资源配置格式，例如服务的启/停、定时任务、设备自动挂载、网络配置、设备配置、虚拟内存配置等。而 `Systemd` 通过不同的通过文件的后缀名来区分这些配置文件，之前我们写的 `.service` 文件便是其中的一种。下面是 `Systemd` 所支持的12种 `Unit` 文件类型。

|后缀名|作用|
|---:|:----|
|.automount | 用于控制自动挂载文件系统。自动挂载即当某一目录被访问时系统自动挂载该目录，这类 unit 取代了传统 Linux 系统的 autofs 相应功能
|.device | 对应 `/dev` 目录下设备，主要用于定义设备之间的依赖关系
|.mount | 定义系统结构层次中的一个挂载点，可以替代过去的 `/etc/fstab` 配置文件
|.path | 用于监控指定目录变化，并触发其他 unit 运行
|.scope | 这类 unit 文件不是用户创建的，而是 Systemd 运行时自己产生的，描述一些系统服务的分组信息|
|.service | 封装守护进程的启动、停止、重启和重载操作，是最常见的一种 unit 类型
|.slice | 用于描述 `cgroup` 的一些信息，极少使用到，一般用户就忽略它吧
|.snapshot | 这种 unit 其实是 `systemctl snapshot` 命令创建的一个描述 Systemd unit 运行状态的快照
|.socket | 监控系统或互联网中的 socket 消息，用于实现基于网络数据自动触发服务启动
|.swap | 定义一个用于做虚拟内存的交换分区
|.target| 用于对 unit 进行逻辑分组，引导其他 unit 的执行。它替代了 SysV 中运行级别的作用，并提供更灵活的基于特定设备事件的启动方式。例如 `multi-user.target` 相当于过去的运行级别`5`，而 `bluetooth.target` 在有蓝牙设备接入时就会被触发
|.timer | 封装由`system`的里面由时间触发的动作, 替代了 `crontab` 的功能

这些琳琅满目的种类，几乎囊括了系统管理的大部分的日常工作内容，一致的配置格式和操作方法使得即便普通的 Linux 系统使用者和软件开发者也能够很快的上手修改系统的配置，妈妈再也不用担心我们把系统弄挂了。其实这些配置文件类型中，真正经常需要修改的并不多，并且这篇文章只打算对其中最常用的，也是之前一直在写的 `.service` 类型展开说明。主要出于篇幅考虑，不过，既然格式都统一了，只要将一种配置类型用熟了，其他的配置学习来还不是分分钟的事啦。

需要再次强调的是，Unit 文件按照 Systemd 约定，应该被放置在指定的3个系统目录之一。这3个目录是有优先级的，依照下面表格，越靠上的优先级越高，因此在几个目录中有同名文件的时候，只有优先级最高的目录里的那个会被使用。
|路径 | 说明
|:---|:---|
|`/etc/systemd/system`| 系统或用户提供的配置文件
|`/run/systemd/system`| 软件运行时生成的配置文件
|`/usr/lib/systemd/system`| 系统或第三方软件安装时添加的配置文件

由于这里的最后一个目录在 CoreOS 中是属于系统的只读分区，因此在 CoreOS 中，第三方软件如果安装时可能需要特别处理，将配置的 Unit 文件放到 `/run/systemd/system`目录中。索性 CoreOS 本来也不推荐直接在系统上安装软件，人家特意把系统分区做成只读这个意思就已经很明确了。一些确实需要安装在 CoreOS 上的软件比如 Deis，它的 `.service` 服务配置都是放到 `/run/systemd/system`目录里面的。

##### Service文件
开门见山，直接来看两个实际的服务配置文件吧。

第一个配置是 CoreOS 系统中 Docker 服务的 `Unit` 文件，路径是`/usr/lib/systemd/system/docker.service`，可以看到其中的内容相当精简易读。
```
[Unit]
Description=Docker Application Container Engine
Documentation=http://docs.docker.com
After=docker.socket early-docker.target network.target
Requires=docker.socket early-docker.target
[Service]
Environment=TMPDIR=/var/tmp
Environment=DOCKER_OPTS='--insecure-registry="0.0.0.0/0"'
EnvironmentFile=-/run/docker_opts.env
LimitNOFILE=1048576
LimitNPROC=1048576
ExecStart=/usr/lib/coreos/dockerd --daemon --host=fd:// $DOCKER_OPTS
[Install]
WantedBy=multi-user.target
```

第二个配置的写法风格与前一个有所差异，但同样的内容清晰，条理明确。这个配置来自 CoreOS 的一篇文档，作用是启动一个 Apache 服务容器然后将服务的运行信息注册到 Etcd 中。

（注意，这篇文档原文中的示例中似乎有一个错误，在启动 docker 时，`ExecStart` 中的命令参数 `-p 80:80` 应当为 `-p 8081:80`，下面代码已修正）
```yaml
[Unit]
Description=My Advanced Service
After=etcd.service
After=docker.service
[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run --name apache1 -p 8081:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStartPost=/usr/bin/etcdctl set /domains/example.com/10.10.10.123:8081 running
ExecStop=/usr/bin/docker stop apache1
ExecStopPost=/usr/bin/etcdctl rm /domains/example.com/10.10.10.123:8081
[Install]
WantedBy=multi-user.target
```

仔细观察着两个服务配置，其中有一些很明显的共同点。我们接下来就以这两个 `Unit` 文件为例，一步步的分析一下 `Systemd` 服务配置的写法。

`Service` 的 `Unit` 文件可以分为3个配置区段，其中 `Unit` 和 `Install` 段是所有 `Unit` 文件通用的，用于配置服务（或其他系统资源）的描述、依赖和随系统启动方式。而 `Service` 段则是服务类型的 `Unit` 文件（后缀`.service`）特有的，用于定义服务的具体管理和操作方法。其他的每种配置文件也都会有一个特有的配置段，这就是几种不同 `Unit` 配置文件最明显的区别。

来看看每个配置段常用的参数有哪些。

###### 1.Unit
* `Description`：一段描述这个 Unit 文件的文字，通常只是简短的一句话。
* `Documentation`：指定服务的文档，可以是一个或多个文档的URL路径。
* `Requires`：依赖的其他 Unit 列表，列在其中的 Unit 模块会在这个服务启动的同时被启动，并且如果其中有任意一个服务启动失败，这个服务也会被终止。
* `Wants`：与 `Requires` 相似，但只是在被配置的这个 Unit 启动时，触发启动列出的每个 Unit 模块，而不去考虑这些模块启动是否成功。
* `After`：与 `Requires` 相似，但会在后面列出的所有模块全部启动完成以后，才会启动当前的服务。
* `Before`：与 `After` 相反，在启动指定的任一个模块之前，都会首先确保当前服务已经运行。
* `BindsTo`：与 `Requires` 相似，但是一种更强的关联。启动这个服务时会同时启动列出的所有模块，当有模块启动失败时终止当前服务。反之，只要列出的模块全部启动以后，也会自动启动当前服务。并且这些模块中有任意一个出现意外结束或重启，这个服务会跟着终止或重启。
* `PartOf`：这是一个 `BindTo` 作用的子集，仅在列出的任何模块失败或重启时，终止或重启当前服务，而不会随列出模块的启动而启动。
* `OnFailure`：当这个模块启动失败时，就自动启动列出的每个模块。
* `Conflicts`：与这个模块有冲突的模块，如果列出模块中有已经在运行的，这个服务就不能启动，反之亦然。

上面这些配置中，除了 `Description` 外，都能够被添加多次。比如前面第一个例子中的`After`参数在一行中使用空格分隔指定所有值，也可以像第二个例子中那样使用多个`After`参数，在每行参数中指定一个值。

###### 2.Install  
这个段中的配置与 Unit 有几分相似，但是这部分配置需要通过 `systemctl enable` 命令来激活，并且可以通过 `systemctl disable` 命令禁用。另外这部分配置的目标模块通常是特定启动级别的 `.target` 文件，用来使得服务在系统启动时自动运行。
* `WantedBy`：和前面的 `Wants` 作用相似，只是后面列出的不是服务所依赖的模块，而是依赖当前服务的模块。
* `RequiredBy`：和前面的 `Requires` 作用相似，同样后面列出的不是服务所依赖的模块，而是依赖当前服务的模块。
* `Also`：当这个服务被 `enable/disable` 时，将自动 `enable/disable` 后面列出的每个模块。

上面的两个例子中使用的都是 `“WantedBy=multi-user.target”` 表明当系统以多用户方式（默认的运行级别）启动时，这个服务需要被自动运行。当然还需要 `systemctl enable` 激活这个服务以后自动运行才会生效。关于 Linux 系统启动时的运行级别，可以参看这篇文章。

###### 3. Service
这个段是 `.service` 文件独有的，也是对于服务配置最重要的部分。这部分的配置选项非常多，主要分为服务生命周期控制和服务上下文配置两个方面，下面是比较常用到的一些参数。

服务生命周期控制相关的参数：
* `Type`：服务的类型，常用的有 `simple`（默认类型） 和 `forking`。默认的 `simple` 类型可以适应于绝大多数的场景，因此一般可以忽略这个参数的配置。而如果服务程序启动后会通过 `fork` 系统调用创建子进程，然后关闭应用程序本身进程的情况，则应该将 Type 的值设置为 `forking`，否则 `systemd` 将不会跟踪子进程的行为，而认为服务已经退出。
* `RemainAfterExit`：值为 `true` 或 `false`（也可以写 `yes` 或 `no`），默认为 `false`。当配置值为 `true` 时，`systemd` 只会负责启动服务进程，之后即便服务进程退出了，`systemd` 仍然会认为这个服务是在运行中的。这个配置主要是提供给一些并非常驻内存，而是启动注册后立即退出然后等待消息按需启动的特殊类型服务使用
* `ExecStart`：这个参数是几乎每个 `.service` 文件都会有的，指定服务启动的主要命令，在每个配置文件中只能使用一次。
* `ExecStartPre`：指定在启动执行 `ExecStart` 的命令前的准备工作，可以有多个，如前面第二个例子中所示，所有命令会按照文件中书写的顺序依次被执行。
* `ExecStartPost`：指定在启动执行 `ExecStart` 的命令后的收尾工作，也可以有多个。
* `TimeoutStartSec`：启动服务时的等待的秒数，如果超过这个时间服务任然没有执行完所有的启动命令，则 `systemd` 会认为服务自动失败。这一配置对于使用 `Docker` 容器托管的应用十分重要，由于 `Docker` 第一次运行时可以能会需要从网络下载服务的镜像文件，因此造成比较严重的延时，容易被 `systemd` 误判为启动失败而杀死。通常对于这种服务，需要将 `TimeoutStartSec` 的值指定为 `0`，从而关闭超时检测，如前面的第二个例子。
* `ExecStop`：停止服务所需要执行的主要命令。
* `ExecStopPost`：指定在 `ExecStop` 命令执行后的收尾工作，也可以有多个。
* `TimeoutStopSec`：停止服务时的等待的秒数，如果超过这个时间服务仍然没有停止，`systemd` 会使用 `SIGKILL` 信号强行杀死服务的进程。
* `Restart`：这个值用于指定在什么情况下需要重启服务进程。常用的值有 `no`，`on-success`，`on-failure`，`on-abnormal`，`on-abort` 和 `always`。默认值为 `no`，即不会自动重启服务。这些不同的值分别表示了在哪些情况下，服务会被重新启动，参见下表。

|服务退出原因 | no | always | on-failure | on-abnormal | on-abort | no-success
|:---|:---|:---|:---|:---|:---|:---:|
|正常退出 | | √ | | | | √ |
|异常退出	| | √	| √ | | | |
|启动/停止超时| | √ | √	| √ | | |
|被异常KILL | | √ | √ | √ | √ | | |

* `RestartSec`：如果服务需要被重启，这个参数的值为服务被重启前的等待秒数。
* `ExecReload`：重新加载服务所需执行的主要命令。

服务上下文配置相关的参数：
* Environment：为服务添加环境变量，如前面的第一个例子中所示。
* EnvironmentFile：指定加载一个包含服务所需的环境变量列表的文件，文件中的每一行都是一个环境变量的定义。
* Nice：服务的进程优先级，值越小优先级越高，默认为`0`。`-20`为最高优先级，`19`为最低优先级。
* WorkingDirectory：指定服务的工作目录。
* RootDirectory：指定服务进程的根目录（ / 目录），如果配置了这个参数后，服务将无法访问指定目录以外的任何文件。
* User：指定运行服务的用户，会影响服务对本地文件系统的访问权限。
* Group：指定运行服务的用户组，会影响服务对本地文件系统的访问权限。
* LimitCPU / LimitSTACK / LimitNOFILE / LimitNPROC 等：
  限制特定服务可用的系统资源量，例如 CPU，程序堆栈，文件句柄数量，子进程数量… 不再展开说明了，值的含义可参考 Linux 文档资源配额部分中 `RLIMIT_` 开头的那些参数们。
  列完这么一大推参数的我也是醉了（这些都是常用的参数，不常用的还没写咧），但其实嘛，`Systemd` 的精华也就在此了。再仔细一推敲，这么些冗长的参数之间还是有些规律的，并且大多可以望文生义，因此写 `Unit` 文件的差事本身倒并不让人觉得枯燥。反观过去需要学习N种不同配置格式来管理N种不同的系统资源的方法，`Systemd`的理念实在是先进了太多了。而这些参数云云，大概只有用得多了，才会觉得它们看起来不那么讨厌了吧。

##### Fleet 的 X-Fleet 段
前面讨论的都是 Systemd 使用的 Unit 文件。在这个系列的 Fleet 那篇中，演示了 Fleet 中的服务配置。
Fleet 的 Unit 服务描述文件，实际上就是 Systemd 的 .service 配置文件的翻版。但为了方便服务在集群环境的自适应管理，Fleet 在 Systemd 的 Unit 配置基础上添加了一个 `X-Fleet` 段，专门用于描述服务应该被分配到集群的哪些节点启动。它的可用参数只有`5`个，可以请出来一一亮相。
* `MachineID`：直接了当的告诉 Fleet 这个服务只能运行在特定的一个节点上，注意这里的值必须是完整的节点 `ID`，这个 `ID` 可以通过 `“fleetctl list-machines -l`” 命令获得。
* `MachineOf`：值是另一个 `.service` 文件，表示当前服务必须运行在与指定的这个服务在同一个节点上。
MachineMetadata：值是一个节点的 `Metadata` 内容，例如 "`region=us-east-1`" 。这些 `Metadata` 是在启动节点时通过 `Cloudinit` 写进去的，具体方法在系列的 `Fleet` 那篇文章有提及。这个参数可以使用多次，或在通过空格分隔将多个值同时传进去。
* `Conflicts`：值是一个 `.service` 文件的，`Conflicts`参数也可以使用多次，并且其值可以使用通配符，例如 `apache*` 表示所有以 “`apache`” 开头的服务。
* `Global`：如果值为 `true`，则这个服务会被部署到集群中符合 `MachineMetadata`限定条件的每一个节点上。注意，当 `Global` 值为 `true` 时，除了 `MachineMetadata`外的所有其他约束条件都会被忽略。

前四个参数在 Fleet v0.8 版本前被命名为 `X-ConditionMachineID`、`X-ConditionMachineOf`、`X-ConditionMachineMetadata`和 `X-Conflicts`，这些写法现在已经停止使用了，但仍然可能会在一些早期的文档或网络文章中出现，如果看见了，淡定的飘过吧。

##### Unit模板
在现实中，往往有一些应用需要被复制多份运行，例如在一个负载均衡实例后面运行的多个相同的服务实例。但是按照之前的例子，每个服务都需要一个单独的 Unit 文件，这样复制多份相同文件的做显然不便于服务的管理。为此 Systemd 定义了一种特殊的 Service Unit文件，称为 Unit 模板。

模板文件的主要特点是，文件名以@符号结尾，而启动的时候指定的Unit名称为模板名称附加一个参数字符串。例如，将之前的例子第二个 Unit 文件修改为可以用于启动多个实例的模板。

###### 1.首先修改文件名，添加一个`@`符号
例如原来的文件名是 `apache.service`，那么可以将它修改为 `apache@.service`，这样做的目的是表面这个文件是一个模板文件。而在服务启动时可以在@后面放置一个用于区分服务实例的附加字符串参数，通常这个参数会使用监听的端口号或使用的控制台`TTY`编号等。例如 “`systemctl start apache@8080.service`”。

###### 2.然后修改 Unit 文件内容
Unit 文件中可以获取服务启动时的附加参数，因此通常需要修改 Unit 文件中不应固定的部分，例如服务监听的 `IP` 和端口，替换为从附加参数中获取。
```yaml
[Unit]
Description=My Advanced Service Template
After=etcd.servicedocker.service
[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache%i
ExecStartPre=-/usr/bin/docker rm apache%i
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run --name apache%i -p %i:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStartPost=/usr/bin/etcdctl set /domains/example.com/%H:%i running
ExecStop=/usr/bin/docker stop apache1
ExecStopPost=/usr/bin/etcdctl rm /domains/example.com/%H:%i
[Install]
WantedBy=multi-user.target
```

仔细观察一下变化了的地方，上面使用到了占位符 `%H` 和 `%i`，常用的占位符有`6`种（一共`19`种，其余不怎么常用的查文档吧），这些占位符会在 Unit 启动时被实际的值动态的替换掉。
|占位符 | 作用 |
|:---|:---|
|%n | 完整的 Unit 文件名字，包括 `.service` 后缀名
|%m	| 实际运行的节点的 `Machine ID`，适合用来做Etcd路径的一部分，例如 `/machines/%m/units`
|%b	| 作用有点像 `Machine ID`，但这个值每次节点重启都会改变，称为 `Boot ID`
|%H	| 实际运行节点的主机名
|%p	| Unit 文件名中在 `@` 符号之前的部分，不包括 `@` 符号
|%i | Unit 文件名中在 `@` 符号之后的部分，不包括 `@` 符号和 `.service` 后缀名
顺带一提，这些参数中除了 `%i` 以外，同样可以用于非模板的 `Unit` 文件中。`%p` 在普通 `Unit` 文件中会被动态替换为服务名称去掉 `.service` 后缀的名字。

###### 3.启动 Unit 模板的服务实例
模板服务的启动对于 `Systemd` 和 `Fleet` 大致相同。

Systemd 的情况略简单一些，只需要运行时加上后缀参数。例如 “`systemctl start apache@8080.service`”。Systemd 首先会在其特定的目录下寻找名为 `apache@8080.service`的文件，如果没有找到，而文件名中包含`@`字符，它就会尝试去掉后缀参数匹配模板文件。例如没有找到`apache@8080.service`，那么Systemd会找到`apache@.service`，并将它通过模板文件中实例化。

Fleet 没有特定的 Unit 文件存放目录，不过在通过` fleetctl start` 或 `fleetctl submit` 命令指定 Unit 文件路径时加上后缀参数，Fleet 同样会自动匹配去掉后缀参数后的模板文件。例如 “`fleetctl submit <img src="https://latex.codecogs.com/gif.latex?{HOME}&#x2F;apache@8080.service`”，就会匹配到 `"/>{HOME}` 目录下面的 `apache@.service` 模板文件。

后续综合案例的文章中，还会结合实际例子详细的介绍模板的使用场景。

##### 小结
这一篇的内容略为零碎，主要是对 CoreOS 中的系统资源和服务起着管理作用的 Unit 配置文件做了比较深入的说明。特别是最后的 Unit 模板部分在一定程度上赋予了服务横向拓展的能力，在实际的项目环境中使用得相当普遍。这些系统管理方面的技巧，需要一定的实战磨练才能体会其中的好处。

最近有同事问我，介绍了这么多的 CoreOS，能否用一句话来评述一下这个系统，以及它最适用于什么样的应用场景呢？

对于第一个问题，**CoreOS 并不是什么神秘的银弹，它只是一个“理念比较先进（具体见系列第一篇）”并且“对集群和应用容器比较友好”的服务器 Linux 发行版**。有些人会追问，要是把 CoreOS 和其他发行版进行对比哪个好用呢。这个…专注的领域不一样啊，CoreOS 永远也不会替代 Fedora 和 Ubuntu 这些桌面 Linux 发行版的地位，因为它实际上是一个高度精简的没有 GUI 和 x-window 的操作系统（但并不是说 CoreOS 不能够提供需要 GUI 的服务，因为可以在容器中安装 x-window 和 VNC 服务）。

对于第二个问题，其实是没有准确答案的，Linux 系统发行版的选择完全是个人偏好问题。**普遍来说，基于 CoreOS 的自动无缝升级和对容器和集群友好的特性，它会比较适用于需要长期运行，并且具备横向扩展架构，特别是 Micro Service 架构的以对外提供服务为目的的集群** 。但并不是说 CoreOS 就不能用在一般的服务器场景，美国的SaaS云服务网站Iron.io和购物网站Shopify都使用了CoreOS 作为其业务支撑的平台，它们的业务场景除了都使用大规模的集群外，各方面都很不一样。

在接下来的两篇中会连续介绍两种结合 CoreOS 的内置特性实现服务高可用的综合案例，敬请期待。


<span id="coreos09"></span>
## 9. Application Services (1)
CoreOS实践指南（九）：在CoreOS上的应用服务实践（上）

摘要: 在“漫步云端：CoreOS实践指南”系列的前几篇文章中，ThoughtWorks的软件工程师林帆主要介绍了CoreOS及其相关组件和使用，其中已经提到了使用 Unit 文件配置 Systemd 管理的系统服务的方式，本文将结合 CoreOS 的内置特性实现服务高可用的综合案例。

截止到这里，CoreOS的基础部分已经全部介绍完毕，回头看看，其实大部分的篇幅都用在了介绍CoreOS内置服务的使用上。这些内置的服务，一方面来说为集群中的服务管理和通信提供了一种简单和规范的操作方式，但另一方面也确实使得应用服务引入了特定的依赖。所幸的是这些依赖并没有依存于CoreOS的生态链，因为所有的这些内置服务都是开源、独立的，也就是说比如Etcd、Fleet、Docker这些服务完全可以运行在任何其他的现代Linux发行版之上。只是，在CoreOS系统中，这些服务已经全部集成，并且会随系统自动升级，可以放心的使用罢了。

正是由于这些内置的服务，有些事，在哪儿都能做，不过在CoreOS上特省心。

在这个系列的最后几篇里，我们会用一些实际的例子来说明通过CoreOS的便利（其实就是Etcd和Fleet这些内置服务的提供的便利啦）能够完成的服务案例。

##### 案例说明
在一个运行着数十上百种应用服务的集群的运维和应用设计中常常会遇到这样的需求：服务状态的收集，如何在集群的任意节点上快速的获取到整个集群里任意一个服务的状态呢？

这是典型的大型分布式服务监控任务。传统的解决方案一般都需要引入一个额外的监控系统，例如Nagios，并且通常会使用一个集中的数据收集和存储节点。利用在每个节点的客户端收集定制数据，然后发送回收集节点统一处理和展示，其余节点如果需要集群状态，就要到这个收集节点上获取。此外，如果所有的应用服务都是自行研发的，还可以在应用中添加一段代码，提供统一的暴露状态接口，简化数据收集。否则监控系统就需要知道哪些节点上运行了哪些服务，以便如何通过不同的接口和方式来分别获取每个服务的信息，而这个过程往往是繁琐而复杂的。

在CoreOS中，数据存储和分发的事情可以交给Etcd包干了，定时采集监控这件事本身直接让Fleet来搞定，其实需要自己费点劲的事情仅仅是定制一下监控数据的收集方法。我们其实可以给每个应用服务配备一个“秘书服务”，让这个服务一边跟着被监控的应用服务在节点间东奔西走，一边记录应用服务的实时状态（呃，这到底是秘书还是间谍…），这样又省去了传统集中收集数据时判断哪个节点当前运行哪些服务的麻烦。

为了不偏颇特别的应用场景，下面的例子采用一个最简单的服务作为监控的目标 —— 一个啥子都没有部署的纯纯的Apache HTTP应用服务，顺带省去设计定制数据的内容，单纯的检查HTTP服务是否可用。收集数据的定制会包含很多针对具体业务场景相关的细节，不具有普遍共性。最后我们会看到为什么这样看似简单的组合搭配Etcd的集群数据同步能力，随着数量的积累，就能够实现大规模的分布式服务状态监控。

##### 容器化服务
将服务容器化能够使得普通的服务通过Fleet的协助获得跨节点调度的能力。这里将重点放在CoreOS本身的使用上，略过制作Docker镜像的过程，因此直接使用一个Docker官方仓库里的Apache镜像 `eboraas/apache`。下面来看个推荐的CoreOS应用服务Unit模板。
```yaml
# apache@.service

[Unit]
Description=Apache web server service listening on port %i

# Requirements
Requires=etcd.service
Requires=docker.service

# Dependency ordering
After=etcd.service
After=docker.service

[Service]
# Let processes take a while to start up (for first run Docker containers)
TimeoutStartSec=0

# Change killmode from "control-group" to "none" to let Docker remove work correctly.
KillMode=none

# Get CoreOS environmental variables
EnvironmentFile=/etc/environment

# Pre-start and Start
## Directives with "=-" are allowed to fail without consequence
ExecStartPre=-/usr/bin/docker kill apache.%i
ExecStartPre=-/usr/bin/docker rm apache.%i
ExecStartPre=/usr/bin/docker pull eboraas/apache
ExecStart=/usr/bin/docker run --name apache.%i -p ${COREOS_PUBLIC_IPV4}:%i:80 eboraas/apache

# Stop
ExecStop=/usr/bin/docker stop apache.%i

[X-Fleet]
# Don't schedule on the same machine as other Apache instances
Conflicts=apache@*.service
```
在系列的上一篇中已经介绍了Unit模板文件，将上面这个配置保存成名为 `apache@.service` 的模板。在启动服务之前，我们先快速的浏览一下这个模板中的各个部分，以便说明怎样修改这个模板以适应具体业务场景的需求。

首先，`[Unit]`段中，`Requires` 列出了这个服务需要依赖的其他用户或系统服务的名字，然后用 `After` 和 `Before`（这里没有）等关键字指明这些服务的启动顺序。CoreOS会等待所有写在 `After` 区域的服务启动完成后再运行当前服务，同时在当前服务启动完成后，唤起所有写在 `Before` 的其他服务。

然后，`[Service]`段中，设置了两个特别的参数 `TimeoutStartSec=0` 和 `KillMode=none`。前一个之前提到过，主要是防止Docker在第一次启动由于下载镜像时间较长而被Systemd认为失去响应而误杀。后一个配置是因为Docker的每个容器都托管于同一个守护进程下面，从而使得容器停止后Systemd依然认为进程没有清理干净，有时会导致下一次启动同名的容器时出现莫名的问题，具体可以参考这篇文档。

接下来，出现了属于CoreOS特别的配置 `EnvironmentFile=/etc/environment`，这里是指定服务读取系统的环境变量文件，这个文件中的变量是在每次系统启动的时候写入的，后面用到的变量 `COREOS_PUBLIC_IPV4` 就是来自这个文件。

最后，`[X-Fleet]`段，可以看到这里配置的 `Conflicts=apache@*.service` 让与当前服务相同的服务进程不要调度到当前这个节点上，这样做是为了实现服务的高可用，在即使出现单节点严重故障的时候其他节点上的相同进程可以继续提供服务。一般来说为了实现高可用，在前级还需要加上反向代理作为负载均衡节点，这种做法已经是企业级服务的标准配备了。

整体看来，对于单独的应用服务而言，这个模板可以说是考虑得相当充分了。

##### 使用Fleet和Etcd监控服务状态
这次内容的主角，秘书服务（请尽情的将它想象为一个貌美如花的美女）出场了。我们来一睹它的芳容。
```yaml
# apache-secretary@%i.service

[Unit]
Description=Monitoring the Apache web server running on port %i

# Requirements
Requires=etcd.service
Requires=apache@%i.service

# Dependency ordering and binding
After=etcd.service
After=apache@%i.service
BindsTo=apache@%i.service

[Service]
# Get CoreOS environmental variables
EnvironmentFile=/etc/environment

# Start
## Test whether service is accessible and then register useful information
ExecStart=/bin/bash -c '\
  while true; do \
    curl -f ${COREOS_PUBLIC_IPV4}:%i; \
    if [ $? -eq 0 ]; then \
      etcdctl set /services/apache/${COREOS_PUBLIC_IPV4} \'{"host": "%H", "ipv4_addr": ${COREOS_PUBLIC_IPV4}, "port": %i}\' --ttl 30; \
    else \
      etcdctl rm /services/apache/${COREOS_PUBLIC_IPV4}; \
    fi; \
    sleep 20; \
  done'

# Stop
ExecStop=/usr/bin/etcdctl rm /services/apache/${COREOS_PUBLIC_IPV4}

[X-Fleet]
# Schedule on the same machine as the associated Apache service
ConditionMachineOf=apache@%i.service
```

感觉现在这个秘书长得有点抽象？呃，那我们来仔细推敲推敲。

首先，不难看出，它也是一个Unit模板（那么多 %i、%H 占位符）。

然后，它的[Unit]段使用了一个特别的限定：`BindsTo=apache@%i.service`。这个关键字表明，这个秘书服务需要随着它监控的 Apache 应用服务一起启动、停止和重启（文档是这样写滴，但其实不包括启动，见后文）。这一点很必要，否则这个秘书就无法正确的汇报所监控的服务状态了。

再往下，`[Service]`段中的 `ExecStart` 和 `ExecStop` 的内容是需要重点说明的地方，待稍后慢慢道来。

最后，`[X-Fleet]`段指明这个服务要与对应的服务始终保持在同一个主机节点上，这也是秘书服务能够获得应用状态的保障。

这样看来，最麻烦的地方无非就是上面这段乱糟糟的 `ExecStart` 命令。先大略的打量一下，这个地方和 `ExecStop` 里面都使用了 `etcdctl`，它们应该是比较关键的内容，先提出来看看。

在 `ExecStart` 中的这个命令，是往 Etcd 数据服务的 `/services/apache/` 目录下写入了一个以当前 Apache 服务所在 IP 命名的键，而键的内容就是秘书所记录的服务信息，包括服务所在的主机名，公网IP和监听的公网网卡端口号。最后的 `TTL` 设置是其中的精彩之处，它确保了当整个服务失效或被迁移到其他节点的时候，这条记录会在30秒内被清除。

```
etcdctl set /services/apache/${COREOS_PUBLIC_IPV4} \'{"host": "%H", "ipv4_addr": ${COREOS_PUBLIC_IPV4}, "port": %i}\' --ttl 30;
```

下面这个命令在 `ExecStart` 和 `ExecStop` 中各出现了一次，它的参数比较清晰，就是移除 `/services/apache/` 下面刚才写入的那个键。
```
etcdctl rm /services/apache/${COREOS_PUBLIC_IPV4};  
```
网上看，整个命令使用一个 `while true` 循环包裹起来，因此除非外部因素结束这个秘书服务，否则这个它定要跟随着被监控的服务一直这么纠缠下去了。

再往下看，有个 `sleep 20` 的行，也就是说，如果在被监控服务运行正常的情况下，每隔 20 秒秘书就会刷新一次服务的状态信息（虽然这个例子里只是IP、端口这些比较固定的信息，实际情况中所监控的信息可能不止这些）。这也使得在正常情况下，Etcd中的数据永远不会由于 TTL 的超时而被清除。

至此，这个 `ExecStart` 的意思也就大致清晰了。其中还没有被提到的那行 curl 命令以及 `etcdctl set` 中需要记录的内容就是实际监控服务需要定制的部分。例子里的 Apache 其实是监控的最简单情况。

好吧，简单归简单，似乎现在可以启动这两个服务来测试一下效果了吧。但是...要挨个启动两个服务？显然这里有些不合理的地方。

##### 关联秘书服务
刚刚在写 Apache 应用服务的时候，由于还没有秘书的存在，我们只考虑了应用服务自己的启动依赖。然而，一个合理的需求是，当应用服务启动时，相应的秘书服务也应该自动启动。还记得刚刚在 `apache-secretary@%i.service` 文件中的 `BindsTo` 那行吗？事实上，单有这个配置只能够实现这个服务随着相应的 Apache 服务的停止和重启，当 Apache 启动时，由于秘书服务的进程还不存在，这儿的 `BindsTo` 是不会生效的。因此，还需要向 Apache 服务中加上秘书服务的依赖以及启动顺序的指定。

```yaml
# Requirements  
Requires=etcd.service  
Requires=docker.service  
Requires=apache-secretary@%i.service    # 增加这行，指明依赖服务名称  

# Dependency ordering  
After=etcd.service  
After=docker.service  
Before=apache-secretary@%i.service    # 增加这行，指定依赖启动顺序  
```

##### 启动
啊，终于到了这个“鸡冻人心”的时刻。

在前一节，介绍过启动Unit模板的方法，只需要在 `fleet` 命令中模板名参数的`@`符号后面加上相应标识字符串就可以了。由于我们在模板中使用了这个标识字符串作为服务在容器外暴露的端口号（这是一种很常用的技巧），因此这个字符串应该使用一个小于`65525`的数字表示。例如：
```
$ fleetctl submit apache@.service apache-secretary@.service  
$ fleetctl load apache@8080.service apache-secretary@8080.service  
$ fleetctl start apache@8080.service  
```

还可以再注册一个Apache 服务到集群中，它会自动运行到不同的节点上（由于`apache@.service`中的`Conflicts`配置）。
```
$ fleetctl load apache@8081.service apache-secretary@8081.service  
$ fleetctl start apache@8081.service  
$ fleetctl list-units | grep apache   
UNIT                            MACHINE                      ACTIVE      SUB  
apache-secretary@8081.service   1af37f7c... /10.132.249.206  active  running  
apache-secretary@8080.service   1af37f7c... /10.132.249.206  active  running  
apache@8081.service             1af37f7c... /10.132.249.206  active  running  
apache@8080.service             1af37f7c... /10.132.249.206  active  running
```

现在在集群中的任意一个节点，通过 Etcd 都可以轻松的获得集群中每一个 Apache 服务的信息。
```
$ etcdctl ls /services/apache/  
/services/apache/10.132.249.212  
/services/apache/10.132.249.206  
$ etcdctl get /services/apache/10.132.249.206  
{"host": "core-01", "ipv4_addr": "10.132.249.206", "port": "8081"}  
```

到目前为止，一切看起来已经很不错了。仔细一想，似乎还有一个美中不足的地方，由于Fleet启动使用了模板的服务必须明确指定标识字符串，因此总是需要在启动命令参数里明确写出每一个服务的名称和标识，并且管理起来并不十分方便。

相比之下，使用非模板的Unit文件则可以使用通配符来一次启动多个服务，因为每个服务对应了磁盘上一个真实的Unit文件。那么可不可以使用 `link` 文件来给同一个模板文件创建多个带标识字符串的别名来充数呢，我们再创建几个服务链接文件，来试试。

```
$ ln -s templates/apache@.service instances/apache@8082.service  
$ ln -s templates/apache@.service instances/apache@8083.service  
$ ln -s templates/apache@.service instances/apache@8084.service  
$ ln -s templates/apache-secretary@.service instances/apache-secretary@8082.service  
$ ln -s templates/apache-secretary@.service instances/apache-secretary@8083.service  
$ ln -s templates/apache-secretary@.service instances/apache-secretary@8084.service  
$ fleetctl start instances/*  
Unit apache@8082.service launched on 14ffe4c3... /10.132.249.212  
Unit apache@8083.service launched on 1af37f7c... /10.132.249.206  
Unit apache@8084.service launched on 9e389e93... /10.132.248.177  
Unit apache-secretary@8082.service launched on 14ffe4c3… /10.132.249.212  
Unit apache-secretary@8083.service launched on 1af37f7c... /10.132.249.206  
Unit apache-secretary@8084.service launched on 9e389e93... /10.132.248.177  
```
可以看到，Fleet欣然接受了这些通过链接创建的替身，并正确的将链接文件的标识字符用于配置相应的应用服务启动。实际上，这种给每个要启动的具体应用实例创建一个链接到真实模板的方式恰恰是CoreOS官方推荐的使用模板方式，它将原本仅仅体现在启动参数里面的服务标识固化为一个个随时可见、可管理的链接，确实是值得推荐的。

###### 模拟故障情景
最后我们快速的模拟一下这种情况：如果，应用服务挂了。将一个服务停止掉（或者你也可以把它残忍的用 `kill` 命令杀掉），按照上面的设计，记录在 Etcd 中的信息应该在 `30` 后由于 `TTL` 超时而被删除。
```
$ fleetctl stop apache@8080.service  
$ fleetctl list-units  
UNIT                          MACHINE                      ACTIVE      SUB  
apache-secretary@8080.service 14ffe4c3... /10.132.249.212  inactive    dead  
apache-secretary@8081.service 1af37f7c... /10.132.249.206  active      running  
apache@8080.service           14ffe4c3... /10.132.249.212  inactive    dead  
apache@8081.service           1af37f7c... /10.132.249.206  active      running  
```

现在再来查询一次服务状态，可以看到记录的 Apache 服务只剩下 `10.132.249.206` 节点的 `8081` 端口那个了。
```
$ etcdctl ls /services/apache/  
/services/apache/10.132.249.206  
$ etcdctl get /services/apache/10.132.249.212  
Error:  100: Key not found (/services/apache/10.132.249.212)  
```

##### 小结
这个案例中，我们虽然仅仅设计了一个单纯的HTTP服务的监控，然而随着需要监控的服务数量增加和集群中服务的流动（节点间迁移）增多，案例中监控策略的复杂度并不会显著的增加，算得上是因为简单所以可靠。分布在各个节点的秘书服务能够很好的适应被监控服务的动态变化，并且对被监控服务具有很低的入侵性（甚至不会限制服务运行在哪里），因此当应用场景变得复杂化、分布化时，其优势会比传统的集中式管理策略体现得更加明显。本质上说，这是在用分布式的思想来解决分布式的问题，自然显得得心应手，驾轻就熟。这种设计思想和微服务架构的精髓相当契合：简单即美。

从这个简单却不失经典的小案例中，可以看出合理的利用CoreOS系统提供的内置服务能够快速的解决不少分布式设计遇到的麻烦。下一篇中，还会再介绍一个同样小巧精致的典型案例在CoreOS中的运用，敬请期待。


<div class="pagebreak"> </div>

<span id="coreos10"></span>
## 10. Application Services (2)
CoreOS实践指南（十）：在CoreOS上的应用服务实践（下）

摘要: 介绍CoreOS为分布式和集群服务带来的便利，在上一篇的分布式服务中应用运行的节点和时间均不确定的问题的服务监控方案的基础上，继续实现将监控结果作为自动配置的反馈，从而配合 CoreOS 的内置服务实现另一个典型的服务器自动配置场景——用于负载均衡的反向代理服务。

在这篇中，会继续接着前次的话题，通过具体的案例，介绍CoreOS为分布式和集群服务带来的便利。在前一个案例中，为了完成采集和管理分布在集群各个节点上的服务状态信息，我们通过 Etcd 的分布式存储特性，设计了一种解决分布式服务中应用运行的节点和时间均不确定的问题的监控方法。在这次的案例中，会在这种服务监控方案的基础上，继续实现将监控结果作为自动配置的反馈，从而配合 CoreOS 的内置服务实现另一个典型的服务器自动配置场景——用于负载均衡的反向代理服务。

##### 应用层负载均衡
应用层负载均衡，又称七层负载均衡（因为其作用在TCP/IP协议栈的第七层），是用于在用户访问到达真正的Web服务之前，通过一个反向代理服务器将请求均匀分发到多个相同的后端逻辑服务器上，从而减轻单个服务节点压力的方法。由于这种方案相对廉价且能够对访问用户透明的扩展网络设备和服务器的带宽、增加吞吐量、加强网络数据处理能力、提高网络的可用性，它已经是数据分流的最常见做法。事实上，我们几乎可以在任何大型网络服务上发现应用层负载均衡的运用。

考虑到例子的实用和延续性，这个案例中选择目前较为流行的 Nginx 作为HTTP反向代理服务。后端的服务使用与前篇文章相同的 Apache Http Server 并通过 Etcd 存储收集到的服务状态信息。从下面张结构图就能够清晰看出整个服务结构的全景，图中的每个服务器图标代表一个系统服务，其中后端的三个Apache服务是运行在各种不同的集群节点上的，而 Nginx 服务运行在这三个节点中的其中一个上。

在这个结构中，Nginx 反向代理的分发策略配置是我们此次需要研究的对象。这个策略主要包括，流量分发的节点地址，各个节点的流量分发比例，访问出错的处理等等。初始状态下，我们假设这个策略加入了所有的三个后端节点，并且平均分配各个节点的流量。然而，后端的 Apache 服务节点并不总是一年365天的每一分钟总是运行正常并且数量恒定不变的。访问过量、软件BUG、硬件故障都有可能使得其中部分节点服务出现暂时性的失效或奔溃，使得实际可用节点减少。反之，当网站服务的用户访问量变得越来越大时，则需要适当增加后端服务节点的数量来缓解集群的负担。但常见的负载均衡服务器（如典型的 Nginx）的请求分发规则并不会根据后端节点的状态和数量变化而自动修改。因此，这个案例的关键部分在于使得 Nginx 依据后端服务节点的数量和可用性，自动适配其收到HTTP请求的分发策略。

PS：其实说Nignx不会自动调节并不十分严谨，虽然Nginx不能自动修改其策略配置，但遇到后端服务连续多次返回错误状态时，Nginx能够自动暂时屏蔽向故障节点转发请求，在指定的时间后再次尝试启用被屏蔽的节点，如果还是连续出错又再次屏蔽，如此反复。然而，这种反复使用实际链接请求来测试节点失效的做法会带来一定的效率损失，同时使得部分用户获得错误响应，不如更新策略配置删除失效节点来得可靠。并且对于后端节点增加的情况更是完全无法自动处理。

##### 自适应反向代理
通过 CoreOS 实现自适应反向代理的思路是比较清晰的。首先，为了让反向代理服务及时的将失效的后端节点从转发列表中移除，每个后端服务需要有自己的状态监控，也就是上篇文章里使用到的秘书服务，将服务的状态信息放入 Etcd 的固定位置。这样当有新的服务节点进入集群时，它的信息也会出现在 Etcd 中并让反向代理服务发现。

有了每个后端服务的状态信息，剩下的就是让反向代理监听 Etcd 数据的变化，然后自动更新策略配置的内容了。这个任务可以通过 Etcd 的 Restful API 或者 etcdctl 工具再写一个新的服务来实现，并不算太麻烦。不过，对于配置文件自动更新这个任务实在是太常见了，社区里已经有了许多成熟的方案提供了更统一和便于管理的做法，其中比较常用的一个是 Confd。

其实呢，介绍到这一步，这篇文章的案底也已经泄露得差不多了。后面的内容在 Google 搜索关键字 “Confd + Nginx” 就可以找到不少相关的介绍，但其中文章质量有些良莠不齐，这里推荐的一篇是 Paul Dixon 博客，后面的内容有些就参考了他的代码。好吧，既然已经走到这里，还是别半途而废啦，一起来继续实现完这个案例的场景吧。

###### 创建Apache服务节点
Apache 服务节点的结构和上一篇中介绍的基本一致。分别使用 Apache 应用服务的 Unit 模板文件 `apache@.service` 和监控 Apache 应用的秘书 Unit 模板文件 `apache-secretary@.service` 来启动。顺便在下面的Unit文件中加了些注释说明，应该不用多说了。
```yaml
# apache@.service

[Unit]
Description=Apache web server service listening on port %i

# 依赖的其他服务项
Requires=etcd.service
Requires=docker.service
Requires=apache-secretary@%i.service

# 依赖启动顺序
After=etcd.service
After=docker.service
Before=apache-secretary@%i.service

[Service]
# 取消启动超时，防止第一次运行时Docker下载镜像导致启动过超时错误
TimeoutStartSec=0

# 让Systemd在服务实例结束时，不要尝试杀死启动服务实例的进程
KillMode=none

# 载入 CoreOS 系统环境变量
EnvironmentFile=/etc/environment

# 启动命令
ExecStartPre=-/usr/bin/docker kill apache.%i
ExecStartPre=-/usr/bin/docker rm apache.%i
ExecStartPre=/usr/bin/docker pull eboraas/apache
ExecStart=/usr/bin/docker run --name apache.%i -p ${COREOS_PUBLIC_IPV4}:%i:80 eboraas/apache

# 结束命令
ExecStop=/usr/bin/docker stop apache.%i

[X-Fleet]
# 将每个服务实例分布在不同的服务器节点上
Conflicts=apache@*.service
```

这里顺便回答一个上篇中遗留的问题。我们通过引入一个额外的秘书服务来监控一个应用服务的可用性，那么秘书服务本身的可用性如何保证呢？是否需要给秘书服务也配备一个附加的监控服务，但这样岂不就陷入了无限的监控链？事实上，由于秘书服务的逻辑非常简单，不会存在并发或内存访问的压力，无故崩溃的可能性很低。通常秘书服务的可用性是自保证的，不过为了避免秘书真的意外私奔（额..我是说..失联），可以给它加上`Restart=on-failure`配置，见下面的Unit文件。
```yaml
# apache-secretary@.service

[Unit]
Description=Monitoring the Apache web server running on port %i

# 依赖的其他服务项
Requires=etcd.service
Requires=apache@%i.service

# 依赖启动顺序
After=etcd.service
After=apache@%i.service
BindsTo=apache@%i.service

[Service]
# 载入 CoreOS 系统环境变量
EnvironmentFile=/etc/environment
Restart=on-failure

# 启动命令
# 测试服务实例是否可用，并更新信息到 Etcd 存储
ExecStart=/bin/bash -c '\
  while true; do \
    curl -f ${COREOS_PUBLIC_IPV4}:%i; \
    if [ $? -eq 0 ]; then \
      etcdctl set /services/apache/${COREOS_PUBLIC_IPV4} \'{"host": "%H", "ipv4_addr": ${COREOS_PUBLIC_IPV4}, "port": %i}\' --ttl 30; \
    else \
      etcdctl rm /services/apache/${COREOS_PUBLIC_IPV4}; \
    fi; \
    sleep 20; \
  done'

# 结束命令
ExecStop=/usr/bin/etcdctl rm /services/apache/${COREOS_PUBLIC_IPV4}

[X-Fleet]
# 让这个服务运行在与其监控的实例同一个服务器节点
ConditionMachineOf=apache@%i.service
```

然后快速的启动三个服务后台 Apache 服务 Unit。
```
$ fleetctl start "apache@8080.service" \
                      "apache-secretary@8080.service" \
                      "apache@8081.service" \
                      “apache-secretary@8081.service" \
                      "apache@8082.service" \
                      "apache-secretary@8082.service"
```
这两个Unit文件的内容在系列的前面已经讲解过。接下来，一起快速浏览一下 Nginx 反向代理节点和 Confd 工具的配置。

###### 创建共享数据服务
作为 CoreOS 最佳实践的一部分，我们应该将 Confd 服务和 Nginx 都运行在各自的容器实例中（当然可以把这两个服务打包到一个容器实例里面，那样会给当下节省不少事情，但带来的将是容器可复用性的降低）。由于 Confd 需要更新 Nginx 的配置，因此需要为这两个容器提供一个共享的目录来存放这个配置文件。最简单的做法是直接在本地创建一个目录，将 Nginx 的所有配置拷贝进去，分别映射这个目录到两个容器里面作为 Nginx 的配置目录和 Confd 的配置生成目录。但这样做就引入了额外手工的操作，为未来可能的自动化部署埋下隐患。一种改进的做法是将这个操作写成一个特别的服务文件，通过 Fleet 统一管理，这样就消除了手工操作的引入。但是这样还是需要对容器暴露一个本地目录，这个目录可能会在容器外被人为有意或无意的被更改或删除。要解决这个问题，可以将容器化精神发扬彻底，索性将这个目录放到一个第三者容器实例里面，这样就真的是“360度无侧漏”了 : D。

根据以上思路，来看下面这个在容器中提供存储空间的服务文件。
```yaml
# confdata.service

[Unit]
Description=Configuration Data Volume Service
After=docker.service
Requires=docker.service

[Service]
EnvironmentFile=/etc/environment
Type=oneshot
RemainAfterExit=yes

ExecStartPre=-/usr/bin/docker rm conf-data
ExecStart=/usr/bin/docker run -v /etc/nginx --name conf-data nginx echo "created new data container"
```
这个服务的特别之处在于 `Type=oneshot` 和 `RemainAfterExit=yes` 这两个地方。前者确保了不论对这个服务执行多少次 `start` 操作，其内容在每个节点上永远仅仅会被执行一次（如果重新执行就要丢数据了）。而后者使得即便这个服务`ExecStart`启动的进程退出了，`Fleet` 依然认为服务正在运行，从而方便其他服务配置依赖。还有一个巧妙的地方是，`ExecStart`中的命令使用的是 `nginx` 的 `Docker` 镜像，其 `/etc/nginx` 目录已经包含了所有的 Nginx 默认配置文件，这样就省去了拷贝配置文件到共享目录的步骤，一举多得。

##### 创建Confd配置监控服务
Confd 是与 CoreOS 无关的一个独立的集群配置监控工具，它可以与多种集群数据存储服务集成，包括 Etcd、Consul等。Confd 通过监控指定的数据来源内容和配置模板生成应用配置，并会在配置内容更新时，自动通知应用重新加载配置。

简单来说，使用它只需要提供两件东西：`Confd`的配置文件（包括数据源、配置文件路径和通知方式等）和生成配置文件的模板。但鉴于在CoreOS上需要通过容器运行服务，我们还需要两个东西：创建容器的 `Dockerfile` 和运行容器实例的 `Unit` 文件。

###### Confd的配置文件
`Confd` 的配置文件通过一种名为 `TOML` 格式声明，其格式有些类似Windows的 `ini `配置格式，但支持配置域的嵌套。
```yaml
[template]

# 应用的配置文件模板名，Confd 会自动到 /etc/conf.d/templates 下面去找这个文件
src = "nginx.conf.tmpl"

# 更新以后的配置文件存放路径
dest = "/etc/nginx/sites-enabled/app.conf"

# 监听的 Etcd 路径，这个路径中的键的内容将可以在配置模板中被访问
keys = [ "/services/apache" ]

# 应用配置文件应有的所有者和权限
owner = "root"
mode = "0644"

# 用来通知应用更新配置的命令
reload_cmd = "echo -e "POST /containers/nginx.service/kill?signal=HUP HTTP/1.0\r\n" | nc -U /var/run/docker.sock"
```

文件注释已经比较清晰了。只是最后的命令需要结合后面启动 Confd 容器的地方，`/var/run/docker.sock` 文件被映射到了主机的Docker套接字文件，参考这篇文章，这条命令的作用相当于 “`/usr/bin/docker kill -s HUP nginx.service`” ，发送一个HUB信号给 Nginx 的服务，但更加可靠。

###### 生成配置文件的模板
根据前面的配置，这个模板生成的文件会被放置到容器的 `/etc/nginx/sites-enabled/app.conf` 路径下面。模板的内容中大括号中的部分会被实际数据相应的替换掉，关于 Confd 模板的详细介绍，可以参考其文档。
```yaml
# nginx.conf.tmpl

upstream apache_pool {
{{ range getvs "/services/apache/*" }}
    server {{ . }};
{{ end }}
}

server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;
    access_log /var/log/nginx/access.log upstreamlog;
    location / {
        proxy_pass http://apache_pool;
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    }
}
```
有了这个两个文件，Confd 的准备工作就已经就绪了。接下来，就将这些文件和 Confd 服务一起打包成一个镜像吧。

###### Confd 容器镜像
Confd 默认会加载 `/etc/confd/conf.d/` 目录下的所有配置文件，并且从 `/etc/confd/templates` 目录中寻找模板文件。我们需要将前面的两个文件放置到容器中正确的目录里。

在当前目录新建名为 `confd/conf.d/` 的目录，将 `nginx.toml` 配置文件复制进去，再新建一个 `confd/templates/` 目录，将 `nginx.conf.tmpl` 模板复制进去。最后在当前目录创建一个内容如下的 Dockerfile 文件。

```Dockerfile
# Confd Dockerfile

FROM ubuntu:14.04

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get -y install curl && \
    curl -o /usr/bin/confd -L https://github.com/kelseyhightower/confd/releases/download/v0.7.1/confd-0.7.1-linux-amd64 && \
    chmod 755 /usr/bin/confd

ADD confd/ /etc/confd

#this environemtn variable needs to be passed in
CMD /usr/bin/confd -interval=60 -node=http://$COREOS_PRIVATE_IPV4:4001
```

使用这个Dockerfile生成Image并提交到镜像仓库中。
```
$ CONFD_IMAGE="用户名/confd-for-nginx"
$ docker build --tag=$CONFD_IMAGE '.'
$ docker push $CONFD_IMAGE
```

###### Confd 服务
下面这个服务文件将启动一个刚刚创建的Confd容器的实例。
```yaml
# confd.service

[Unit]
Description=Configuration Service

# 声明依赖，共享数据的服务必须已经存在
After=confdata.service
Requires=confdata.service

[Service]
EnvironmentFile=/etc/environment
ExecStartPre=-/usr/bin/docker kill %n
ExecStartPre=-/usr/bin/docker rm %n
ExecStartPre=/usr/bin/docker pull lordelph/confd-for-nginx

# 注意这里的 volumes-from 参数
ExecStart=/usr/bin/docker run --rm \
  -e COREOS_PRIVATE_IPV4=${COREOS_PRIVATE_IPV4} \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --volumes-from=conf-data \
  --name %n \
  lordelph/confd-for-nginx

ExecStop=/usr/bin/docker stop -t 3 %n
Restart=on-failure

[X-Fleet]
# 必须与共享数据容器在同一个节点
MachineOf=confdata.service
```

这里需要特别注意的是容器启动时的几个参数，
* `-e COREOS_PRIVATE_IPV4=<img src="https://latex.codecogs.com/gif.latex?{COREOS_PRIVATE_IPV4}`  将容器外的变量传递到了容器内部，在这个容器启动时会需要它
* ` -v /var/run/docker.sock:/var/run/docker.sock`  将容器外的 Docker 套接字映射到了容器中的同名文件，这样就可以给其他容器发信号了
* ` --volumes-from=conf-data`  引入 `conf-data` 容器（由数据共享服务创建的）暴露的所有分区，这样容器内部的 `lordelph/confd-for-nginx` 目录其实就被替换成 `conf-data` 容器中的共享目录了一切就绪，现在启动 Confd 服务吧。

```
/> fleet start confd.service
```

###### 创建Nginx反向代理服务
Docker 官方已经提供了 Nginx 的容器镜像，只需要写个服务Unit，直接使用这个镜像就可以了。

```yaml
# nginx.service

[Unit]
Description=Nginx Service
After=confd.service

[Service]
EnvironmentFile=/etc/environment
ExecStartPre=-/usr/bin/docker kill %n
ExecStartPre=-/usr/bin/docker rm %n
ExecStartPre=/usr/bin/docker pull nginx
ExecStart=/usr/bin/docker run --name %n -p 80:80 --volumes-from=conf-data nginx
ExecStop=/usr/bin/docker stop -t 3 %n
Restart=on-failure

[X-Fleet]
# 同样的必须运行在与数据共享容器相同的节点上
MachineOf=confdata.service
```

从 `ExecStart` 这行的参数可以看出，这个服务同样引用了数据共享容器中的共享分区，并且对外暴露了`80`端口，用于通过反向代理访问后端的服务。

将Nginx服务也启动起来，现在整个反向代理加后端负载均衡的架构就完成了，所有的服务均运行在容器中。
```
$ fleetctl start nginx.service

$ fleetctl list-units
UNIT                            MACHINE                      ACTIVE      SUB
apache@8080.service             14ffe4c3... /10.132.249.212  active      running
apache@8081.service             1af37f7c... /10.132.249.206  active      running
apache@8082.service             9e389e93... /10.132.248.177  active      running
apache-secretary@8080.service   14ffe4c3... /10.132.249.212  active      running
apache-secretary@8081.service   1af37f7c... /10.132.249.206  active      running
apache-secretary@8082.service   9e389e93... /10.132.248.177  active      running
confd.service                   9e389e93... /10.132.248.177  active      running
confdata.service                9e389e93... /10.132.248.177  active      exited
nginx.service                   9e389e93... /10.132.248.177  active      running
```

通常我们还会需要在Unit文件里限制 Nginx 服务运行的节点，以便为它绑定域名，这里就去不指定了。可以直接访问 nginx 服务 IP 所在节点的 `80` 端口，获得后端服务的内容。

##### 小结
在这一篇中，介绍通过使用集成到 CoreOS 的内置服务（例如 Etcd）的第三方扩展（例如 Confd），实现特定分布式应用管理的案例。事实上，这个例子中为了演示 CoreOS 实践中的一些技巧，所采用的设计方案并不是所有可行方案中最简单的。对于反向代理这个场景，同样基于 `Etcd` 的 `Vulcand` 工具只需要短短的几行设置就能够搞定。

同时，这个例子也意在说明，使用 CoreOS 系统时，我们应当尽量养成一些正确的习惯，例如将所有应用服务放置到容器中运行，使用 `Dockerfile` 创建和管理容器镜像，每个容器镜像尽可能的只提供单一服务，将共享数据的存储同样容器化、服务化等等。总结来说，CoreOS 系统为了方便集群服务的操控，一方面提供了标准的数据存储、服务调度机制，另一方面也限制了将服务不加隔离的运行于系统之上带来的应用间强耦合的隐患。如果我们能够恰当的遵循这些好的实践方法，在 CoreOS 中设计出具备原生的分布式特性的服务将变为一件理所当然的事情。