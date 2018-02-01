## 部署Spring Boot应用 (Deploying Spring Boot applications）

Spring Boot灵活的打包选项为部署应用提供多种选择，你可以轻易的将Spring Boot应用部署到各种云平台，容器镜像（比如Docker）或虚拟/真实机器。

本章节覆盖一些比较常见的部署场景。

### 1.部署到云端

对于大多数流行云PaaS（平台即服务）提供商，Spring Boot的可执行jars就是为它们准备的。这些提供商往往要求你带上自己的容器；它们管理应用的进程（不特别针对Java应用程序），所以它们需要一些中间层来将你的应用适配到云概念中的一个运行进程。

两个流行的云提供商，Heroku和Cloud Foundry，采取一个打包（’buildpack’）方法。为了启动你的应用程序，不管需要什么，buildpack都会将它们打包到你的部署代码：它可能是一个JDK和一个java调用，也可能是一个内嵌的webserver，或者是一个成熟的应用服务器。buildpack是可插拔的，但你最好尽可能少的对它进行自定义设置。这可以减少不受你控制的功能范围，最小化部署和生产环境的发散。

理想情况下，你的应用就像一个Spring Boot可执行jar，所有运行需要的东西都打包到它内部。

本章节我们将看到在“Getting Started”章节开发的[简单应用](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#getting-started-first-application)是怎么在云端运行的。

#### 1.1 Cloud Foundry

如果不指定其他打包方式，Cloud Foundry会启用它提供的默认打包方式。Cloud Foundry的Java buildpack对Spring应用有出色的支持，包括Spring Boot。你可以部署独立的可执行jar应用，也可以部署传统的.war形式的应用。

一旦你构建了应用（比如，使用mvn clean package）并安装了cf命令行工具，你可以使用下面的cf push命令（将路径指向你编译后的.jar）来部署应用。在发布一个应用前，确保你已登陆cf命令行客户端。


查看cf push文档获取更多可选项。如果相同目录下存在manifest.yml，Cloud Foundry会使用它。

就此，cf开始上传你的应用：

查看cf push文档获取更多可选项。如果相同目录下存在manifest.yml，Cloud Foundry会使用它。

就此，cf开始上传你的应用：


#### 1.2 Heroku


#### 1.3 OpenShift

#### 1.4 Amazon Web Services (AWS)

#### 1.5 Boxfuse and Amazon Web Services

#### 1.6 Google App Engine


### 2.安装Spring Boot应用

除了使用 java -jar 运行Spring Boot应用，制作在Unix系统完全可执行的应用也
是可能的，这会简化常见生产环境Spring Boot应用的安装和管理。在Maven中添加
以下plugin配置可以创建一个"完全可执行"jar：
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <executable>true</executable>
    </configuration>
</plugin>
```

对于Gradle等价的配置如下：
```
springBoot {
    executable = true
}
```
然后输入 `./my-application.jar` 运行应用（ `my-application` 是你的artifact
name）。
>完全可执行jars在文件前内嵌了一个额外脚本，目前不是所有工具都能接受这种
形式，所以你有时可能不能使用该技术。

>默认脚本支持大多数Linux分发版本，并在CentOS和Ubuntu上测试过。其他平
台，比如OS X和FreeBSD，可能需要使用自定义 `embeddedLaunchScript`。

>当一个完全可执行jar运行时，它会将jar的目录作为工作目录。


#### 2.1 Unix/Linux服务

你可以使用 `init.d` 或 `systemd` 启动Spring Boot应用，就像其他Unix/Linux服务那样。

**2.1.1 安装为init.d服务(System V)**

如果你配置Spring Boot的Maven或Gradle插件产生一个[完全可执行jar](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#deployment-install)，并且没有使用自定义的 `embeddedLaunchScript`，那你的应用可以作为 `init.d` 服务使用。只要简单的建立jar到 `init.d` 的符号连接就能获取标准的 `start`，`stop`，`restart`和`status`命令支持。

该脚本支持以下特性：
* 以拥有该jar文件的用户启动服务。
* 使用`/var/run/<appname>/<appname>.pid`跟踪应用的PID。
* 将控制台日志输出到`/var/log/<appname>.log`。

假设你在`/var/myapp`目录安装了一个Spring Boot应用，只需要建立符号连接就能将Spring Boot应用安装成`init.d`服务：  
`$ sudo ln -s /var/myapp/myapp.jar /etc/init.d/myapp`

一旦安装成功，你就可以像平常那样启动和停止服务，例如，在一个基于Debian的系统：  
`$ service myapp start`

> 如果应用启动失败，检查下`/var/log/<appname>.log`中的错误日志。

你也可以标识应用使用标准的操作系统工具自启动，例如，在Debian上：  
`$ update-rc.d myapp defaults <priority>`

***保护init.d服务***

>The following is a set of guidelines on how to secure a Spring Boot application that’s being run as an init.d service. It is not intended to be an exhaustive list of everything that should be done to harden an application and the environment in which it runs.

当使用root用户启动`init.d`服务时，默认的执行脚本将以拥有该jar文件的用户来运行应用。你最好不要使用root启动Spring Boot应用，也就是你的应用jar文件拥有者不能是`root`，而是创建一个特定用户运行应用，并使用`chown`指定该用户拥有jar文件，示例：
`$ chown bootapp:bootapp your-app.jar`  

本示例中，默认执行脚本将使用`bootapp`用户运行应用。

>注 为减少应用用户账号冲突，你可以考虑防止它使用登陆shell，例如将账号shell设置为`/usr/sbin/nologin`。

你也要采取措施防止修改应用jar文件，首先配置jar文件权限只能被拥有者读取和执行，不能写入：  
`$ chmod 500 your-app.jar`

然后，你也应该采取措施限制应用或账号运行时的冲突造成的损坏。如果攻击者获取访问权，他们可能会让jar文件可写并改变它的内容，使用chattr让它变为不可变是唯一的保护措施：  
`$ sudo chattr +i your-app.jar`  
这会防止任何用户修改jar文件，包括root。

如果root用户用来控制应用服务，并且你[使用`.conf`文件](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#deployment-script-customization-conf-file) 自定义它的启动，该`.conf`文件将被root用户读取和评估，因此它也需要保护。使用`chmod`改变文件权限只能被拥有者读取，然后使用`chown`改变文件拥有者为root：
```
$ chmod 400 your-app.conf
$ sudo chown root:root your-app.conf
```

**2.1.2 安装为Systemd服务**

Systemd是System V init系统的继任者，很多现代Linux分发版本都在使用，尽管你可以继续使用`init.d`脚本，但使用`systemd` ‘service’脚本启动Spring Boot应用是有可能的。

假设你在`/var/myapp`目录下安装一个Spring Boot应用，为了将它安装为一个`systemd`服务，你需要按照以下示例创建一个脚本，比如命名为`myapp.service`，然后将它放到`/etc/systemd/system`目录下：
```
[Unit]
Description=myapp
After=syslog.target

[Service]
User=myapp
ExecStart=/var/myapp/myapp.jar
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```
>记得根据你的应用改变`Description`，`User`和`ExecStart`字段。

>注意 `ExecStart` 字段不声明脚本动作命令, 这意味着运行命令是缺省的.

注意跟作为`init.d`服务运行不同，使用`systemd`这种方式运行应用，PID文件和控制台日志文件表现是不同的，必须在‘service’脚本配置正确的字段，具体参考[service unit configuration man page](http://www.freedesktop.org/software/systemd/man/systemd.service.html)。

使用以下命令标识应用自动在系统boot上启动：  
`$ systemctl enable myapp.service`

具体详情可参考`man systemctl`。

**2.1.3 自定义启动脚本**

Maven或Gradle插件生成的默认内嵌启动脚本可以通过很多方法自定义，对于大多数开发者，使用默认脚本和一些自定义通常就足够了。如果发现不能自定义需要的东西，你可以使用`embeddedLaunchScript`选项生成自己的文件。

* **在脚本生成时自定义**  

  自定义写入jar文件的启动脚本元素是有意义的，例如，为init.d脚本提供description，既然知道这会展示到前端，你可能会在生成jar时提供它。  

  为了自定义写入的元素，你需要为Spring Boot Maven或Gradle插件指定`embeddedLaunchScriptProperties`选项。

  以下是默认脚本支持的可代替属性：

* **在脚本运行时自定义**

  对于需要在jar文件生成后自定义的项目，你可以使用环境变量或配置文件。

  默认脚本支持以下环境变量：

  |变量    | 描述|
  |--------|:-----:|
  |MODE|操作的模式|

  ，默认值依赖于jar构建方式，通常为auto（意味着它会尝试通过检查它是否为init.d目录的软连接来推断这是不是一个init脚本）。你可以显式将它设置为service，这样stop|start|status|restart命令就可以工作了，或如果你只是想在前台运行该脚本那只需run
USE_START_STOP_DAEMON
如果start-stop-daemon命令可用，它将被用来控制该实例，默认为true
PID_FOLDER
pid文件夹的根目录（默认为/var/run）
LOG_FOLDER
存放日志文件的文件夹（默认为/var/log）
CONF_FOLDER
读取.conf文件的文件夹
LOG_FILENAME
存放于LOG_FOLDER的日志文件名（默认为<appname>.log）
APP_NAME
应用名，如果jar运行自一个软连接，脚本会猜测它的应用名。如果不是软连接，或你想显式设置应用名，这就很有用了
RUN_ARGS
传递给程序的参数（Spring Boot应用）
JAVA_HOME
默认使用PATH指定java的位置，但如果在$JAVA_HOME/bin/java有可执行文件，你可以通过该属性显式设置
JAVA_OPTS
JVM启动时传递的配置项
JARFILE
在脚本启动没内嵌其内的jar文件时显式设置jar位置
DEBUG
如果shell实例的-x标识有设值，则你能轻松看到脚本的处理逻辑

>`PID_FOLDER`，`LOG_FOLDER`和`LOG_FILENAME`变量只对`init.d`服务有效。对于`systemd`等价的自定义方式是使用‘service’脚本。

如果`JARFILE`和`APP_NAM`E出现异常，上面的设置可以使用一个`.conf`文件进行配置。该文件预期是放到跟jar文件临近的地方，并且名字相同，但后缀为`.conf`而不是`.jar`。例如，一个命名为`/var/myapp/myapp.jar`的jar将使用名为`/var/myapp/myapp.conf`的配置文件：  

myapp.conf
```
JAVA_OPTS=-Xmx1024M
LOG_FOLDER=/custom/log/folder
```

>如果不喜欢配置文件放到jar附近，你可以使用`CONF_FOLDER`环境变量指定文件的位置。

想要学习如何正确的保护文件可以参考[the guidelines for securing an init.d service](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#deployment-initd-service-securing)。



#### 2.2 Microsoft Windows服务

在Window上，你可以使用[winsw](https://github.com/kohsuke/winsw)启动Spring Boot应用。  
这里有个[单独维护的示例](https://github.com/snicoll-scratches/spring-boot-daemon)为你演示了怎么一步步为Spring Boot应用创建Windows服务。


### 3.接下来阅读什么

打开[Cloud Foundry](http://www.cloudfoundry.com/)，[Heroku](https://www.heroku.com/)，[OpenShift](https://www.openshift.com/)和[Boxfuse](https://boxfuse.com/)网站获取更多Paas能提供的特性信息。这里只提到4个比较流行的Java PaaS提供商，由于Spring Boot遵从基于云的部署原则，所以你也可以自由考虑其他提供商。

下章节将继续讲解[Spring Boot CLI](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#cli)，你也可以直接跳到[build tool plugins](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins)。
