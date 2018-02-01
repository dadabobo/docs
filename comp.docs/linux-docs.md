## Linux


### Linux开发环境

虚化与容器
* docker
* Vagrant

配置管理
* ansible
* chef
* puppet

构建工具
* Maven
* Gradle
* Git

中间件
* Apache2
* MySQL
* Tomcat
* redis
* Zookeeper
* storm

开发包
* jdk
* jedis
* boost
* protobuf
* sparsehash


### 常用linux命令

**xargs**  
`xargs`是给命令传递参数的一个过滤器，也是组合多个命令的一个工具。它把一个数据流分割为一些足够小的块，以方便过滤器和命令进行处理。  
通常情况下，`xargs`从管道或者`stdin`中读取数据，但是它也能够从文件的输出中读取数据。`xargs`的默认命令是echo，这意味着通过管道传递给`xargs`的输入将会包含换行和空白，不过通过`xargs`的处理，换行和空白将被空格取代。  
`xargs` 是一个强有力的命令，它能够捕获一个命令的输出，然后传递给另外一个命令，下面是一些如何有效使用`xargs` 的实用例子。
1. 当你尝试用`rm` 删除太多的文件，你可能得到一个错误信息：`/bin/rm Argument list too long.` 用`xargs` 去避免这个问题  
	`# find ~ -name ‘*.log’ -print0 | xargs -0 rm -f`
2. 获得`/etc/` 下所有`*.conf` 结尾的文件列表，有几种不同的方法能得到相同的结果.
   下面的例子仅仅是示范怎么实用`xargs` ，在这个例子中实用 `xargs`将`find` 命令的输出传递给`ls -l`  
	`# find /etc -name "*.conf" | xargs ls –l`
3. 假如你有一个文件包含了很多你希望下载的URL, 你能够使用`xargs` 下载所有链接  
	`# cat url-list.txt | xargs wget -c`
4. 查找所有的jpg 文件，并且压缩它  
	`# find / -name *.jpg -type f -print | xargs tar -cvzf images.tar.gz`
5. 拷贝所有的图片文件到一个外部的硬盘驱动  
	`# ls *.jpg | xargs -n1 -i cp {} /external-hard-drive/directory`



### ubuntu下软件安装卸载与查看

**软件包管理应用总结**

```
apt-cache search        --(package 搜索包)
apt-cache show          --(package 获取包的相关信息，如说明、大小、版本等)
apt-get install         --(package 安装包)
apt-get install         --(package --reinstall 重新安装包)
apt-get -f install      --(强制安装, "-f = --fix-missing"当是修复安装吧...)
apt-get remove          --(package 删除包)
apt-get remove --purge  --(package 删除包，包括删除配置文件等)
apt-get autoremove --purge  --(package 删除包及其依赖的软件包+配置文件等)
apt-get update          --更新源
apt-get upgrade         --更新已安装的包
apt-get dist-upgrade    --升级系统
apt-get dselect-upgrade --使用 dselect 升级
apt-cache depends       --(package 了解使用依赖)
apt-cache rdepends      --(package 了解某个具体的依赖,当是查看该包被哪些包依赖吧...)
apt-get build-dep       --(package 安装相关的编译环境)
apt-get source          --(package 下载该包的源代码)
apt-get clean && apt-get autoclean  --清理下载文件的存档 && 只清理过时的包
apt-get check           --检查是否有损坏的依赖
dpkg -S filename        --查找filename属于哪个软件包
apt-file search filename    --查找filename属于哪个软件包
apt-file list packagename   --列出软件包的内容
apt-file update         --更新apt-file的数据库
dpkg --info "软件包名"  --列出软件包解包后的包名称.
dpkg -l     --列出当前系统中所有的包.可以和参数less一起使用在分屏查看. (类似于rpm -qa)
dpkg -l |grep -i "软件包名"  --查看系统中与"软件包名"相关联的包.
dpkg -s     --查询已安装的包的详细信息.
dpkg -L     --查询系统中已安装的软件包所安装的位置. (类似于rpm -ql)
dpkg -S     --查询系统中某个文件属于哪个软件包. (类似于rpm -qf)
dpkg -I     --查询deb包的详细信息,在一个软件包下载到本地之后看看用不用安装(看一下呗).
dpkg -i     --手动安装软件包(这个命令并不能解决软件包之前的依赖性问题)
dpkg -r     --卸载软件包.不是完全的卸载,它的配置文件还存在.
dpkg -P     --全部卸载(但是还是不能解决软件包的依赖性的问题)
dpkg -reconfigure       --重新配置
```

**apt管理工具常用命令**

```
apt-cache   加上不同的子命令和参数的使用可以实现查找,显示软件,包信息及包信赖关系等功能.
apt-cache stats   显示当前系统所有使用的Debain数据源的统计信息.
apt-cache search +"包名", 可以查找相关的软件包.
apt-cache show   +"包名", 可以显示指定软件包的详细信息.
apt-cache depends +"包名",  可以查找软件包的依赖关系.
apt-get upgrade   更新系统中所有的包到最新版
apt-get install   安装软件包
apt-get --reindtall install   重新安装软件包
apt-get remove  卸载软件包
apt-get --purge remove  完全卸载软件包
apt-get clean   清除无用的软件包
```
在用命令apt-get install之前,是先将软件包下载到/var/cache/apt/archives中,之后再进行安装的.所以我们可以用apt-get clean清除/var/cache/apt/archives目录中的软件包.

**源码包安装**
```
apt-cache showsrc           查找看源码包的文件信息(在下载之前)
apt-get source              下载源码包.
apt-get build-dep +"包名"   构建源码包的编译环境.
```

**清除处于rc状态的软件包**
```
dpkg -l |grep ^rc|awk '{print $2}' |tr ["\n"] [" "] | sudo xargs dpkg -P -
```

**Linux 安装 JDK 8**
1. 从 oracle官网下载Linux版本JDK
2. 解压安装到 /usr/lib/jvm， 建立连接
	```
	wangwg@linux:~$ sudo ln -s jdk1.8.0_11 java-8
	```
3. 设置环境变量
	```
	export JAVA_HOME=/usr/lib/jvm/java-8
	export JRE_HOME=${JAVA_HOME}/jre
	export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
	export PATH=${JAVA_HOME}/bin:$PATH
	```
	保存退出，并通过命令使脚本生效：
	```
	wangwg@linux:~$ source ~/.bashrc
	```
4. 配置默认JDK版本
	```
	wangwg@linux:~$ sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-8/bin/java 300
	wangwg@linux:~$ sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/java-8/bin/javac 300
	wangwg@linux:~$ sudo update-alternatives --config java
	```
